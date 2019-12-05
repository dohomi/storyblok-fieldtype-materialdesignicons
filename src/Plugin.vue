<template>
    <div>
        <div class="uk-form-row">
            <div style="text-align: right" v-if="model.name">
                <a @click="unselect"><small>(<i style="color:darkred" class="uk-icon uk-icon-remove" /> remove
                    selection)</small></a>
            </div>
            <div class="select select--inline" :class="{'select--open':openSelect}">
                <a class="select__btn ellipsis" @click.stop="openSelect=!openSelect">
                    <template v-if="model && model.name">
                        <svg viewBox="0 0 24 24" style="width: 24px;height: 24px">
                            <path :d="this.listSvgItems.find(i => i.label === model.name) && this.listSvgItems.find(i => i.label === model.name).path"></path>
                        </svg>
                        <small>&nbsp; -- {{model.name}}</small>
                    </template>
                    <span v-else>
                        Please select...
                    </span>
                </a>

                <div class="select__dropdown" :style="{'display':openSelect?'block':'none'}">
                    <div class="select__type-search">
                        <input type="text" class="uk-width-1-1 js-term-input" v-model="search" placeholder="Search">
                    </div>
                    <div style="max-height: 200px; overflow-y: auto; ">
                        <template v-if="listSvgItems.length > 0">
                            <div class="select__opt-box"
                                 v-for="item in listSvgItems"
                                 :key="item.id">
                                <div @click="selectSvgItem(item)">
                                    <svg viewBox="0 0 24 24" style="width: 24px;height: 24px">
                                        <path :d="item.path" />
                                    </svg>
                                </div>
                            </div>
                        </template>
                    </div>
                </div>
            </div>
        </div>
    </div>
</template>

<script>
  // source of material icon list:
  // Credits: https://gist.githubusercontent.com/AmirOfir/daee915574b1ba0d877da90777dc2181/
  // import lazyLoadCss from 'lazyload-css'
  // import iconObject from './material-icon-list'
  import * as Mdi from '@mdi/js'

  const camelCaseToDash = (str) => str.replace(/([a-zA-Z])(?=[A-Z])/g, '$1-').toLowerCase()

  const objMap = Object.keys(Mdi).map(item => ({
    id: item,
    label: camelCaseToDash(item).replace('mdi-', ''),
    path: Mdi[item]
  }))

  objMap.slice(0, 10)

  export default {
    mixins: [window.Storyblok.plugin],
    methods: {
      unselect () {
        this.$set(this.model, 'name', null)
      },
      selectSvgItem (item) {
        this.$set(this.model, 'name', item.label)

        this.openSelect = false
      },
      initWith () {
        return {
          // needs to be equal to your storyblok plugin name
          plugin: 'material-icons-selector'
        }
      },
      pluginCreated () {
        // eslint-disable-next-line
        console.log('View source and customize: https://github.com/storyblok/storyblok-fieldtype')
      }
    },
    computed: {
      listSvgItems () {
        if (this.search) {
          return objMap.filter(i => i.label.includes(this.search))
        }
        return objMap
      }
    },
    data () {
      return {
        search: '',
        openSelect: false
      }
    },
    watch: {
      'model': {
        handler: function (value) {
          this.$emit('changed-model', value)
        },
        deep: true
      }
    }
  }
</script>
