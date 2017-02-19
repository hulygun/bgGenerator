<template lang="pug" src='./background.pug' />
<style lang="stylus">
  #gen-bg
    position absolute
    left 0
    top 0
    z-index 0
</style>
<script>
    import Vue from 'vue';

    const sqrt = Math.sqrt(2);

    function getRandomInt(min, max) {
      return Math.floor(Math.random() * (max - min) + min);
    }

    function getSecondSize(height, edge) {
      return sqrt*height+edge;
    }

    function genTranslate(size_w, size_r, is_long=false) {
      var translateY = Math.min.apply(this, size_r)/sqrt;
      var min = (is_long) ? -Math.abs(size_r[0] - size_r[1]) * sqrt : 0;
      var max = (is_long) ? Math.max.apply(this, size_w) - Math.min.apply(this, size_r)*sqrt : Math.max.apply(this, size_w) + Math.abs(size_r[0] - size_r[1]) * sqrt;
      var translateX = getRandomInt(min, max + 1);
      return [translateX, -translateY];
    }

    function genRect(size, fill) {
      var out, h, w, translate;

      if (getRandomInt(0,2)) {
        h = getRandomInt(size[1]/4,size[1]*2/3);
        w = getSecondSize(size[1], h);
        translate = genTranslate(size, [w,h], true);
      } else {
        w = getRandomInt(size[1]/4, size[0]/2);
        h = getSecondSize(size[1], w);
        translate = genTranslate(size, [w,h], false);
      }
      return {
        height:h,
        width:w,
        transform: 'translate('+translate.join(" ")+') rotate(45)',
        filter: 'url(#pipe-shadow)',
        fill: fill
      }
    }
    function colorMaker(hex, lum) {
      hex = String(hex).replace(/[^0-9a-f]/gi, '');
      if (hex.length < 6) {
        hex = hex[0]+hex[0]+hex[1]+hex[1]+hex[2]+hex[2];
      }

      lum = lum || 0;
      var rgb = "#", c, i;
      for (i = 0; i < 3; i++) {
        c = parseInt(hex.substr(i*2,2), 16);
        c = Math.round(Math.min(Math.max(0, c + (c * lum)), 255)).toString(16);
        rgb += ("00"+c).substr(c.length);
      }
      return rgb;
    }

    function getRandomColor(hex) {
      var persent = getRandomInt(-8, 6)/10;
      return colorMaker(hex, persent);
    }

    var rectEl = Vue.component('r', {
      props: ['data'],
      render: function(createElement) {
        return createElement('rect', {attrs:this.data});
      }
    });

    export default {
        props: ['count', 'color'],
        name: 'background',
        data: function() {
          return {
            viewBox: '0 0 0 0',
            rects: [],
            size: []
          }
        },
        components: {
          'r': rectEl
        },
        mounted: function(){
            var sizes = [this.$el.clientWidth, this.$el.clientHeight], i = 0;
            this.viewBox = '0 0 ' + sizes.join(' ')
            this.rects.push({x:0, y:0, width: sizes[0], height: sizes[1], fill:this.bgFill})
            while (i < this.count) {
              this.rects.push(genRect(sizes, getRandomColor(this.color)));
              i++;
            }
            this.size = sizes

        },
        computed: {
          bgFill: function() {
            return getRandomColor(this.color);
          },
        }
    }
</script>
