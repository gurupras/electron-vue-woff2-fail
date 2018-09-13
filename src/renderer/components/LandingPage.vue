<template>
  <div id="wrapper">
    <main>
      <div>
          This project uses
          <a href="https://npmjs.com/package/@gurupras/vue-file-explorer" target="_blank">@gurupras/vue-file-explorer</a>
          which internally uses
          <a href="https://npmjs.com/package/@gurupras/file-icons-js" target="_blank">@gurupras/file-icons-js</a>.
          <p>
            The <code>@gurupras/file-icons-js</code> library imports a few <code>woff2</code> files in its CSS.<br>
            These files fail to be imported properly in this electron project.
          </p>
        </span>
      </div>

      <div style="margin-top: 5em;">
        <vue-file-explorer v-for="mount in mountPoints" :key="mount.name"
                :absolute-path="mount.absolutePath"
                :is-directory="mount.isDirectory"
                :name="mount.name"
                :children="mount.children"
                @selected="onSelect"
                @update-directory="onUpdateDirectory"/>
      </div>
    </main>
  </div>
</template>

<script>
  import VueFileExplorer from '@gurupras/vue-file-explorer'

  export default {
    name: 'landing-page',
    components: { VueFileExplorer },
    data () {
      return {
        selected: [],
        mountPoints: [
          {
            name: '/home',
            isDirectory: true,
            children: [
              {
                name: 'Downloads',
                isDirectory: true,
                children: [
                  {
                    name: 'icon-pack.zip',
                    isDirectory: false
                  },
                  {
                    name: 'README.md',
                    isDirectory: false
                  },
                  {
                    name: 'form-37742.docx',
                    isDirectory: false
                  }
                ]
              },
              {
                isDirectory: true,
                name: 'Workspace',
                children: [
                  {
                    name: 'file-browser',
                    isDirectory: true,
                    children: [
                      {
                        name: 'src',
                        isDirectory: true,
                        children: [
                          {
                            name: 'assets',
                            isDirectory: true,
                            children: [
                              {
                                name: 'favicon.ico',
                                isDirectory: false
                              }
                            ]
                          },
                          {
                            name: 'components',
                            isDirectory: true,
                            children: [
                              {
                                name: 'entry.vue',
                                isDirectory: false
                              }
                            ]
                          },
                          {
                            name: 'views',
                            isDirectory: true,
                            children: []
                          },
                          {
                            name: 'App.vue',
                            isDirectory: false
                          },
                          {
                            name: 'main.js',
                            isDirectory: false
                          },
                          {
                            name: 'router.js',
                            isDirectory: false
                          },
                          {
                            name: 'store.js',
                            isDirectory: false
                          }
                        ]
                      }
                    ]
                  }
                ]
              },
              {
                absolutePath: `D:\\Videos\\2016\\vacation.mp4`,
                isDirectory: false,
                name: 'vacation.mp4'
              }
            ]
          },
          {
            name: '/usr',
            isDirectory: true,
            children: [
              {
                name: 'lib',
                isDirectory: true,
                children: [
                  {
                    name: 'libau.so',
                    isDirectory: false
                  }
                ]
              }
            ]
          }
        ]
      }
    },
    methods: {
      open (link) {
        this.$electron.shell.openExternal(link)
      },
      clearAllSelected () {
        this.selected.forEach(entry => (entry.dataset.selected = 'false'))
        this.selected = []
      },
      onSelect ({ event, paths, component }) {
        var ignoreSelect = false
        var entries = [event.target.closest('.entry')]

        if (event.shiftSelect) {
        // Ensure that this element and other selected elements have the same parent
        // If not, ignore this click
        // Since we do this for every multi-select, it is sufficient to just check
        // the parent of the 0th element
          const entry = entries[0]
          const parent = entry.parentNode.closest('.entry')
          // Find all contiguous entries between the last selected entry and the currently selected entry
          // First, get the top-most selected entry
          const lastSelectedEntry = this.selected.splice(-1)[0]
          try {
            const children = parent.children
            const childrenArray = Array.from(children)
            const lastSelectedEntryIdx = childrenArray.indexOf(lastSelectedEntry.parentNode)
            const currentIndex = childrenArray.indexOf(entry.parentNode)
            const minIdx = Math.min(lastSelectedEntryIdx, currentIndex)
            const maxIdx = Math.max(lastSelectedEntryIdx, currentIndex)
            entries = childrenArray.map((entry, idx) => idx >= minIdx && idx <= maxIdx ? entry.querySelector('.entry') : undefined).filter(x => !!x)
          } catch (e) {
          }
        }

        if (!event.multiSelect) {
        // Clear out the selected array
          this.clearAllSelected()
        }

        if (ignoreSelect) {
          return
        }
        entries.forEach(entry => {
          entry.dataset.selected = 'true'
          this.selected.push(entry)
        }, this)
      },
      onUpdateDirectory (data) {

      }
    }
  }
</script>

<style>
  @import url('https://fonts.googleapis.com/css?family=Source+Sans+Pro');

  * {
    box-sizing: border-box;
    margin: 0;
    padding: 0;
  }

  body { font-family: 'Source Sans Pro', sans-serif; }

  #wrapper {
    background:
      radial-gradient(
        ellipse at top left,
        rgba(255, 255, 255, 1) 40%,
        rgba(229, 229, 229, .9) 100%
      );
    height: 100vh;
    padding: 60px 80px;
    width: 100vw;
  }

  #logo {
    height: auto;
    margin-bottom: 20px;
    width: 420px;
  }

  .center {
    margin-left: 50%;
    margin-right: 50%;
  }
</style>
