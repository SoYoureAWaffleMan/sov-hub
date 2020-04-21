<template>
  <div>
    <div v-if="state === 'error'" class="error-msg flex-stack-center with-top-padding">
      <h2>Something bad happened, awkward</h2>
      <img src="@/assets/img/embarrassed-black.svg" class="with-bottom-margin">
      <h2>Try reloading</h2>
    </div>
    <div v-else class="view-event flex-stack-center">
      <img :src="imgSrc" :alt="event.name" ref="img" class="anim-fade-in">
      <h1
        class="text-center"
        :class="{ opaque : event.imageAllowOverlay === false, 'long' : event.name && event.name.length > 20}">
        {{event.name}}
      </h1>
      <time class="text-center"  :class="{ opaque : event.imageAllowOverlay === false}" :datetime="event.startDateString">
        {{event.startDateString | shortDate }} {{event.startTimeString}}
      </time>
      <a class="button stream with-bottom-margin" :href="ticketHref"></a>
      <p v-for="(line, index) in descriptionLines" :key="index" v-html="line"></p>
    </div>
  </div>
</template>

<script>
import axios from 'axios'
import { get } from 'lodash-es'
import { getImgixUrlForElement } from '@/lib/util.js'

export default {
  name : 'Event',

  data : function() {
    return {
      event  : {},
      state  : null,   // 'loading', 'loaded' or 'error'
      imgSrc : null,
    }
  },

  computed : {
    ticketHref() {
      // Note - `url` property is a URI not a URL *shakes fist*
      // Also note - tickets provided by SaveourVenue should contain a raw url, not an Ents24 buy link. Should.
      let href = get(this.event,'ticketSummary.url')

      if(!href) {
        return ''
      }

      // Double check for ents24 "/ticket/buy" URIs  & handle gracefully
      if(href.slice(0,7) === '/ticket') {
        return 'https://ents24.com'+href
      }

      return href
    },
    /**
     * Break the desc (inc. line breaks) into an array of HTML strings
     */
    descriptionLines(){
      let desc = get(this.event,'description', '')
      let descLines = desc.split("\n")
      return descLines.filter(line => !!line)
    }
  },

  mounted() {
    this.state = 'loading'

    const params = {
      id : this.$route.params.id
    }

     axios.get('https://www.ents24.com/internal-api/entity/event', {params})
      .then(response => {
        this.event = get(response, 'data.data')
        this.refreshImgSrc()
        this.state = 'loaded'
      })
      .catch(() => this.state = 'error')

    window.addEventListener('resize', this.refreshImgSrc);
  },

  destroyed(){
    window.removeEventListener('resize', this.refreshImgSrc);
  },

  methods : {
    get,
    refreshImgSrc() {
      if(!this.$refs['img']) {
        return
      }

      this.imgSrc = getImgixUrlForElement(this.event.imgixUrl, this.$refs['img'])
    }
  }
}
</script>

<style lang="scss" scoped>
@import '@/assets/scss/size.scss';

$max-img-wrapper-width : 1000px;
$max-img-height-tablet : 350px;
$max-img-height-tablet : 450px;

.view-event {
  display        : flex;
  flex-direction : column;
}

img{
  width      : 100%;
  height     : $max-img-height-tablet;
  max-width  : $max-img-wrapper-width;
  mask-image : linear-gradient(to bottom, black 15%, transparent 90%);

  @include for-tablet-portrait-up {
    height : $max-img-height-tablet;
  }
}

h1 {
  color      : black;
  margin-top : -6rem;
  max-width  : $max-img-wrapper-width;

  &.long {
    font-size : 2.5rem
  }
}

time {
  text-transform : uppercase;
  font-weight    : bold;
}

.opaque {
  background     : white;
  // Masking is fun...
  mix-blend-mode : hard-light;
}

.button.stream {
  height     : 100px;
  width      : 200px;
  background : url('../assets/img/ents24-stream-diamond.svg') center center no-repeat;

  &:hover{
    filter: contrast(1.1);
  }
}

.error-msg {
  margin : auto;

  img {
    max-width : 200px;
    opacity   : .8;
  }
}
</style>