<template>
  <div role="tree" :class="{'tree': true, 'tree-loading': this.loading}">
    <template v-if="filter && matches.length == 0" >
      <div class="tree-filter-empty">{{ opts.filter.emptyText }}</div>
    </template>
    <template v-else>
      <ul class="tree-root">
        <template v-if="opts.filter.plainList && matches.length > 0">
          <node
            v-for="node in matches"
            v-if="node.visible()"

            :key="node.id"
            :node="node"
            :options="opts"
          />
        </template>
        <template v-else>
          <node
            v-for="node in model"
            v-if="node.visible()"

            :key="node.id"
            :node="node"
            :options="opts"
          />
        </template>
      </ul>
    </template>
  </div>
</template>

<script>
  import TreeNode from '@/components/TreeNode'
  import TreeMixin from '@/mixins/TreeMixin'
  import Tree from '@/lib/Tree'

  const defaults = {
    multiple: true,
    checkbox: false,
    checkOnSelect: false,
    autoCheckChildren: true,
    parentSelect: false,
    keyboardNavigation: true,
    paddingLeft: 24,
    fetchData: null,
    propertyNames: null,
    deletion: false,
    onFetchError: function(err) { throw err }
  }

  const filterDefaults = {
    emptyText: 'Nothing found!',
    matcher(query, node) {
      return new RegExp(query, 'i').test(node.text)
    },
    plainList: false,
    showChildren: true
  }

  export default {
    name: 'Tree',
    components: {
      'node': TreeNode
    },

    mixins: [TreeMixin],

    provide: _ => ({
      tree: null
    }),

    props: {
      data: {},

      options: {
        type: Object,
        default: _ => ({})
      },

      filter: String
    },

    watch: {
      filter (term) {
        this.tree.filter(term)
      }
    },

    data () {
      // we should not mutating a prop directly...
      // that's why we have to create a new object
      // TODO: add method for changing options
      let opts = Object.assign({}, defaults, this.options)

      opts.filter = Object.assign(
        {},
        filterDefaults,
        opts.filter
      )

      return {
        model: null,
        tree: null,
        loading: false,
        opts,
        matches: []
      }
    }
  }
</script>

<style>
  .tree {
    overflow: auto;
  }

  .tree-root,
  .tree-children {
    list-style: none;
    padding: 0;
  }

  .tree > .tree-root,
  .tree > .tree-filter-empty {
    padding: 3px;
    box-sizing: border-box;
  }
</style>
