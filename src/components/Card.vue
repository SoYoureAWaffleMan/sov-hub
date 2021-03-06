<template>
  <li class="card" :class="cardClass" >
    <a :href="getEventHref()" @click.prevent="gotoEvent" :class="{opaque : item.imageAllowOverlay === false}">
      <img :src="imgSrc" :alt="imgAlt" ref="img" class="anim-fade-in">
      <h3 :class="{ 'long' : this.item.name.length > 20 }">
        {{ item.name }}
      </h3>
      <div v-if="venueName" class="subtitle">
        Supporting <strong>{{ venueName }}</strong>
      </div>
      <div v-if="item.startTimeString" class="summary" >
        {{ item.startIso | shortDate }} <span>{{ item.startTimeString }}</span>
      </div>
    </a>
    <div v-if="item.isOnline || item.hasOnlineEvents" class="marker --marker-stream"/>
  </li>
</template>

<script>
import { get } from 'lodash-es'
import { getImgixUrlForElement } from '@/lib/util.js'

export default {
  name: 'Card',
  props: {
    item: {
      type: Object,
      required : true,
    }
  },

  data : function() {
    return {
      imgSrc : null,
      imgAlt : '',
    }
  },

  mounted() {
    this.imgSrc = getImgixUrlForElement(this.item.imgixUrl, this.$refs['img'])
    this.imgAlt = this.item.name
  },

  computed: {
    venueName(){
      const name = this.item.venueSummary.nameShort || this.item.venueSummary.name

      // *Rolls eyes*
      if (!name || name.toLowerCase().includes('online')) {
        return null
      }

      return get(this.item, 'venueSummary.name', 'moose')
    },
    cardClass() {
      return '--status-'+this.item.status
    }
  },

  methods : {
    // Hard-coded event URL to prevent publicPath from being prepended by router / router-link
    getEventHref(){
      return `#/event/${this.item.id}`
    },
    gotoEvent(){
      this.$router.push({ name: 'event', params: { id: this.item.id }})
    }
  }
}
</script>

<style lang="scss" scoped>
@import '@/assets/scss/size.scss';
@import '@/assets/scss/color.scss';

$gap-internal                 : .5rem;
$card-subtitle-color          : $color-sov-salmon;
$card-summary-color           : $color-sov-apple-green;
$card-summary-cancelled-color : grey;

.card {
  background-color : black;
  flex             : 1 0 0 ;
  height           : 250px;
  margin           : 1px;
  max-width        : 320px;     // MAX default (esp. relevant in final row which may contain a single card)
  min-width        : 150px;     // MIN default
  overflow         : hidden;
  position         : relative;
  text-align       : left;

  // Increase card min/max width from 600px
  @include for-tablet-portrait-up {
    height    : 250px;
    min-width : 200px;
    max-width : 400px;
  }

  // Increase card min/max width from 900px
  @include for-tablet-landscape-up {
    height    : 280px;
    min-width : 250px;
  }

  a {
    text-decoration  : none;
    background-color : #333;
    color            : white;
    display          : flex;
    flex-direction   : column;
    height           : 100%;
    justify-content  : flex-end; // Push children to the bottom
    outline-offset   : -2px;
    overflow         : hidden;
    position         : relative; // Contain absolutely-positioned elements
    width            : 100%;
    z-index          : 0;

    > * {
      z-index : 1; // above ::before pseudo element (gradient overlay)
    }

    &:before {
      background : linear-gradient(to bottom, transparent 15%, black 90% );
      content    : '';
      display    : block;
      height     : 100%;
      position   : absolute;
      top        : 0;
      width      : 100%;
    }

    &.opaque:before {
      background: linear-gradient(to bottom, transparent 0%, black 70% );
    }

    img {
      // Encountered an issue on movema demo where height was being set to auto by an id selector!?
      // `#content .entry-content img { height:auto }`
      // Likely dumb shit I wrote, but just in case it's endemic I've importanted dimensions here.
      // It's a word.
      height          : 100% !important;
      width           : 100% !important;
      object-fit      : cover; // Server-only version will not have well-fitted image dimensions
      object-position : center;
      position        : absolute;
      top             : 0;
      z-index         : -1; // below ::before pseudo element (gradient overlay) and content
    }

    h3, .subtitle, .summary {
      margin-bottom : $gap-internal;
      padding       : 0 $gap-internal;
    }
  }

  h3 {
    text-transform : uppercase;
    max-width      : 100%;
    overflow-wrap  : break-word;
    text-shadow    : 0 0 8px black;
    word-wrap      : break-word;
    font-size      : 1.5rem;
    line-height    : 1.7rem;

    @include for-tablet-portrait-up {
      &:not(.long) {
        font-size   : 1.7rem;
        line-height : 2rem;
      }
    }

    &.long {
      font-size   : 1.2rem;
      line-height : 1.5rem;
    }
  }

  .subtitle {
    color : $card-subtitle-color;
  }

  .summary {
    color         : $card-summary-color;
    white-space   : nowrap;
    text-overflow : ellipsis;
    overflow      : hidden;
  }
}

.card.--status-cancelled,
.card.--status-postponed {
  img {
    filter : sepia(100%);
  }

  .subtitle {
    color : $card-summary-cancelled-color;
  }
}

.card.--status-cancelled .subtitle:after {
  content : '- Cancelled'
}

.card.--status-postponed .subtitle:after {
  content : '- Postponed'
}

.marker {
  position : absolute;
  top      : 5px;
  left     : 5px;
  padding  : 5px;
  height   : 50px;
  width    : 70px;

  &.--marker-stream {
    background : url('../assets/img/ents24-stream-diamond.svg') center/contain no-repeat;
  }
}
</style>
