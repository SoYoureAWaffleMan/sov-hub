<template>
  <div id="app">
    <router-view/>
  </div>
</template>

<style lang="scss">
@import '@/assets/scss/size.scss';
@import '@/assets/scss/color.scss';

// Base tags & spacing
body {
  margin      : 0;
  font-family : Helvetica, Arial, sans-serif;
  font-size   : 18px;
  color       : #333;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;


  p, ul, hr, h1, h2, h3 {
    font-weight : normal;
    margin      : 0;
  }

  p, ul, hr, h1, h2, h3, time, .with-bottom-margin {
    margin-bottom : 2rem;
  }

  .with-top-padding {
    padding-top : 2rem;
  }

  h1 {
    font-size   : 3rem;
    line-height : 3rem;
  }

  h1, h2, h3, p, .with-side-padding {
    padding-left : 1rem;
    padding-right : 1rem;
  }

  p, .with-max-width {
    max-width: 800px;
  }

  p {
    line-height: 1.5rem;
    text-align: left;
  }

  a {
    color : $color-sov-link-green;
  }
}

//Text classes
.dull {
  opacity: .8;
}

.auto-center {
  margin-left  : auto;
  margin-right : auto;
}

.text-center {
  text-align: center;
}

.text-left {
  text-align: left;
}

.text-italic {
  font-style: italic;
}

.text-break {
  overflow-wrap : break-word;
  word-wrap     : break-word;
  hyphens       : auto;
  overflow      : hidden;
}

.text-break-long-anchors {

  a{
    display        : inline-block;
    vertical-align : bottom;
    max-width      : 340px;
    overflow       : hidden;
    white-space    : nowrap;
    text-overflow  : ellipsis;

    @include for-tablet-portrait-up {
      max-width : 400px;
    }
}
}

// Layout
.flex-stack-center {
  display        : flex;
  flex-direction : column;
  align-items    : center;
  text-align     : center;

  .stretched {
    align-self: stretch;
  }

  .centered {
    align-self: center;
  }
}

// Animation
@keyframes fade-in {
  0%   { opacity : 0 }
  100% { opacity : 1 }
}

.anim-fade-in {
  animation-duration : 1s;
  animation-name     : fade-in;
}

@keyframes pause-fade-in {
  0%   { opacity : 0 }
  50%  { opacity : 0 }
  100% { opacity : 1 }
}

.anim-pause-fade-in {
  animation-duration : 2s;
  animation-name     : pause-fade-in;
}

</style>
