<template>
  <div class='pswp' tabIndex='-1' role='dialog' aria-hidden='true'>
    <div class='pswp__bg'/>
    <div class='pswp__scroll-wrap'>
      <div class='pswp__container'>
        <div class='pswp__item'/>
        <div class='pswp__item'/>
        <div class='pswp__item'/>
      </div>
      <div class='pswp__ui pswp__ui--hidden'>
        <div class='pswp__top-bar'>
          <div class='pswp__counter'/>
          <button class='pswp__button pswp__button--close'
            title='Cerrar (Esc)'/>
          <button class='pswp__button pswp__button--share'
            title='Compartir'/>
          <button class='pswp__button pswp__button--fs'
            title='Pantalla completa'/>
          <button class='pswp__button pswp__button--zoom'
            title='Zoom'/>
          <div class='pswp__preloader'>
            <div class='pswp__preloader__icn'>
              <div class='pswp__preloader__cut'>
                <div class='pswp__preloader__donut'/>
              </div>
            </div>
          </div>
        </div>
        <div
          class='pswp__share-modal pswp__share-modal--hidden pswp__single-tap'>
          <div class='pswp__share-tooltip'/>
        </div>
        <button class='pswp__button pswp__button--arrow--left'
          title='Anterior (flecha izquierda)'/>
        <button class='pswp__button pswp__button--arrow--right'
          title='Siguiente (flecha derecha)'/>
        <div class='pswp__caption'>
          <div class='pswp__caption__center'/>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import events from './events.js'
import PhotoSwipeFn from 'photoswipe'
import PhotoSwipeUIDefault from 'photoswipe/dist/photoswipe-ui-default.js'
import 'photoswipe/dist/photoswipe.css'
import 'photoswipe/dist/default-skin/default-skin.css'

export default {
  name: 'v-photoswipe',
  props: {
    isOpen: {
      type: Boolean,
      default: false
    },
    items: {
      type: Array,
      required: true
    },
    options: {
      type: Object,
      default: {}
    }
  },
  watch: {
    items: {
      handler: function (val, oldVal) {
        if (this.pswp && this.isOpen) {
          /* this.pswp.items.length = 0
          val.forEach((item) => {
            this.pswp.items.push(item)
          }) */
          // this.pswp.items = this.items = val（指向同一个对象）
          // 所以当props items更新后 this.pswp.items随之自动更新
          this.pswp.invalidateCurrItems()
          this.pswp.updateSize(true)
        }
      },
      deep: true
    },
    isOpen (val, oldVal) {
      if (val) {
        this.openPhotoSwipe(this.items, this.options)
      } else {
        this.close()
      }
    }
  },
  methods: {
    openPhotoSwipe (items, options) {
      let pswpElement = this.$el
      this.pswp = new PhotoSwipeFn(pswpElement, PhotoSwipeUIDefault, items, options)
      events.forEach(e => {
        this.pswp.listen(e, (...args) => {
          args.unshift(this)
          this.$emit(e, [...args])
        })
      })
      this.pswp.init()
    },
    close () {
      if (this.pswp) {
        this.pswp.close()
      }
    }
  },
  mounted () {
    if (this.isOpen) {
      this.openPhotoSwipe(this.items, this.options)
    }
  },
  beforeDestroy () {
    this.close()
  }
}
</script>
<style>
  .pswp--open{
    position: fixed;
    top:0;
    left: 0;
    z-index: 100;
  }
  .pswp--open .pswp__bg{
    opacity: .9 !important;
  }
  .pswp--open .pswp__scroll-wrap .pswp__ui{
    position: static;
  }
  .pswp--open .pswp__scroll-wrap .pswp__ui .pswp__caption .pswp__caption__center{
    text-align: center;
  }
</style>
