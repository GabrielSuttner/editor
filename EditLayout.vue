<template>
  <div class="relative">
    <link rel="stylesheet" type="text/css" src="./editor.css" />
    <transition name="fadeFast">
      <div class="fixed z-10 sm:rounded sm:shadow-blur-glow editLayout" v-if="seeEdit">
        <!-- This is the header bar -->
        <div
          class="flex items-center justify-between shadow-md px-2 sm:rounded-t h-12 bg-gray-200 text-gray-700 z-2 relative"
        >
          <div>
            <!-- Left section of header -->
            <!-- 
              File dropdown
            -->
            <div class="relative inline-block mx-px cursor-pointer">
              <div
                class="py-1 px-3 rounded-sm sm:hover:bg-gray-400"
                :class="{ 'bg-gray-300': showOptions }"
                @click="showOptions = !showOptions"
              >
                File
              </div>
              <transition name="scaleDown">
                <div
                  v-if="showOptions"
                  class="absolute top-2 left-0 bg-gray-100 rounded rounded-tl-none shadow-lg"
                >
                  <ul class="withIcons m-1">
                    <li
                      @click="(showUploader = true), (showOptions = false)"
                      class="grid grid-cols-2 px-3 py-2 hover:bg-gray-300"
                    >
                      <font-awesome-icon icon="plus" class="text-lg" />
                      <div>Image</div>
                    </li>
                    <li @click="save" class="grid grid-cols-2 px-3 py-2 hover:bg-gray-300">
                      <font-awesome-icon :icon="['far', 'save']" class="text-lg" />
                      <div>Save</div>
                    </li>
                    <li
                      @click="
                        save();
                        back();
                      "
                      class="grid grid-cols-2 px-3 py-2 hover:bg-gray-300"
                    >
                      <font-awesome-icon :icon="['far', 'save']" class="text-lg" />
                      <div>Save & Exit</div>
                    </li>
                    <li @click="deleteLayout" class="grid grid-cols-2 px-3 py-2 hover:bg-gray-300">
                      <font-awesome-icon :icon="['far', 'trash-alt']" class="text-lg" />
                      <div>Delete</div>
                    </li>
                    <li
                      @click="
                        showModal = true;
                        showOptions = false;
                      "
                      class="grid grid-cols-2 px-3 py-2 hover:bg-gray-300"
                    >
                      <font-awesome-icon icon="arrow-left" class="text-lg" />
                      <div>Exit</div>
                    </li>
                  </ul>
                </div>
              </transition>
            </div>

            <!-- 
              Layout dropdown
            -->
            <div class="relative inline-block mx-px cursor-pointer">
              <div
                class="py-1 px-3 rounded-sm sm:hover:bg-gray-400"
                :class="{ 'bg-gray-300': showTemplates }"
                @click="showTemplates = !showTemplates"
              >
                Layout
              </div>
              <transition name="scaleDown">
                <div
                  v-if="showTemplates"
                  class="absolute top-2 left-0 bg-gray-100 rounded rounded-tl-none shadow-lg"
                >
                  <ul
                    v-for="(template, index) in templates"
                    :key="index"
                    @click="changeTemplate(template)"
                    class="noIcons hover:bg-gray-300 m-1"
                  >
                    <li>{{ template }}</li>
                  </ul>
                </div>
              </transition>
            </div>

            <!-- 
              Page dropdown
            -->
            <div class="relative inline-block mx-px cursor-pointer">
              <div
                class="py-1 px-3 rounded-sm sm:hover:bg-gray-400"
                :class="{ 'bg-gray-300': showPages }"
                @click="showPages = !showPages"
              >
                Page
              </div>
              <transition name="scaleDown">
                <div
                  v-if="showPages"
                  class="absolute top-2 left-0 bg-gray-100 rounded rounded-tl-none shadow-lg"
                >
                  <ul
                    v-for="(page, index) in pages"
                    :key="index"
                    @click="(layout.page = page), (showPages = false)"
                    class="noIcons hover:bg-gray-300 m-1"
                  >
                    <li>{{ page }}</li>
                  </ul>
                </div>
              </transition>
            </div>

            <!-- 
              Link dropdown 
              link will only be displayed on some layouts
            -->
            <div class="relative inline-block mx-px cursor-pointer" v-if="layout.linkEnabled">
              <div
                class="py-1 px-3 rounded-sm sm:hover:bg-gray-400"
                :class="{ 'bg-gray-300': showLink }"
                @click="showLink = !showLink"
              >
                Link
              </div>
              <transition name="scaleDown">
                <div
                  v-if="showLink"
                  class="absolute top-2 left-0 bg-gray-100 rounded rounded-tl-none shadow-lg"
                >
                  <ul @click="changeLink('internal')" class="noIcons hover:bg-gray-300 m-1">
                    <li>Internal</li>
                  </ul>
                  <ul @click="changeLink('external')" class="noIcons hover:bg-gray-300 m-1">
                    <li>External</li>
                  </ul>
                </div>
              </transition>
            </div>
          </div>

          <div class="flex items-center" style="line-height: 0.5;">
            <!-- Right section of header -->
            <!-- 
              Minimize
             -->
            <div
              @click="seeEdit = false"
              class="inline-block py-2 px-3 rounded-sm sm:hover:bg-gray-400 cursor-pointer"
            >
              <font-awesome-icon :icon="['far', 'window-minimize']" class="" />
            </div>

            <!-- 
              Exit
             -->
            <div
              @click="
                showModal = true;
                closeHeaders();
              "
              class="inline-block py-2 px-3 rounded-sm sm:hover:bg-red-400 text-4xl cursor-pointer"
            >
              &#215;
            </div>
          </div>
          <!-- End of right section -->
        </div>
        <!-- End of header bar -->

        <div class="p-3 positionModule sm:p-4">
          <!-- Headings -->
          <div class="inline-block w-2/3 sm:w-3/4 align-top">
            <input
              name="title"
              v-model="layout.title"
              placeholder="Header"
              class="plain text-5xl sm:text-6xl p-0 w-full"
            />
            <input
              name="subtitle"
              v-model="layout.subtitle"
              placeholder="Subheader"
              class="plain text-2xl ml-1 p-0 w-full"
            />
          </div>

          <!-- Image -->
          <div
            class="relative h-32 sm:h-40 w-1/4 sm:w-32 ml-4 inline-block bg-white overflow-hidden"
          >
            <img v-if="layout.imgPath" class="object-cover z-10" :src="layout.imgPath" />
            <div v-show="layout.imgPath == null" class="h-full border shadow-inner">
              <div class="h-1/2 flex items-end justify-center">
                <div
                  v-if="layout.imgPath == null"
                  @click="showUploader = !showUploader"
                  class="w-12 h-12 rounded-full shadow-md bg-blue-glow-light hover:bg-blue-glow text-white text-lg flex items-center justify-center"
                >
                  <font-awesome-icon icon="plus" />
                </div>
              </div>
              <div class="text-center text-sm p-2">
                Add image
              </div>
            </div>
            <Uploader
              v-if="showUploader"
              @setValues="setValues"
              @uploadFinished="showUploader = false"
              @escape="showUploader = false"
            ></Uploader>
          </div>

          <!-- Editor -->

          <quill-editor
            class="h-full mt-2"
            v-model="layout.content"
            ref="myQuillEditor"
            :toolbarOptions="['bold', 'italic', 'underline', 'strike']"
            :options="editorOptions"
          />
        </div>
      </div>
    </transition>
    <div class="display-doc-wrapper">
      <transition name="fadeFast">
        <button
          v-if="!seeEdit"
          @click="seeEdit = true"
          class="fixed bottom-2 left-0 right-0 w-56 mx-auto py-2 rounded-full z-2 blueBtn shadow-lg"
        >
          Go to layout editor
        </button>
      </transition>
      <component class="w-2/3" :is="layout.template" :layout="layout" :key="layout._id"></component>
    </div>

    <transition name="modal">
      <ModalPopup
        v-if="showLinkModal"
        :title="linkTitle"
        @close="showLinkModal = false"
        @save="alert('hello')"
      >
        <div v-if="linkTitle === 'Internal'">
          <select v-model="internalLink">
            <option v-for="tag in tags" :key="tag._id" :value="tag">{{ tag.name }}</option>
          </select>
          <button class="outline-primary mx-4 px-4" @click="setLinkInternal()">
            Save
          </button>
        </div>
        <div v-else>
          <input
            type="text"
            placeholder="http://example.com"
            class="border"
            v-model="externalLink"
          />
          <button class="outline-primary mx-4 px-4" @click="setLinkExternal()">
            Save
          </button>
        </div>
      </ModalPopup>
    </transition>

    <transition name="modal">
      <ConfirmationModal
        v-if="showModal"
        title="Editor"
        body="Do you want to save changes?"
        :buttons="modalButtons"
        @save="
          save();
          back();
        "
        @leave="back"
        @close="showModal = false"
      >
      </ConfirmationModal>
    </transition>
  </div>
</template>

<script>
import 'quill/dist/quill.snow.css';
import { quillEditor } from 'vue-quill-editor';

import Concise from '../layouts/Concise';
import Lengthy from '../layouts/Lengthy';
import Banner from '../layouts/Banner';
import Hero from '../layouts/Hero';
import Legal from '../layouts/Legal';

import Uploader from '@/components/Uploader.vue';
import ConfirmationModal from '@/components/shared/ConfirmationModal';
import ModalPopup from '@/components/shared/ModalPopup';

export default {
  data: () => ({
    layout: '',
    seeEdit: true,

    //Editor Variables
    editorOptions: {
      debug: 'warn',
      placeholder: 'Type your post...',
      readOnly: false,
      theme: 'snow',
    },
    delta: undefined,

    //Header Variables
    templates: ['Concise', 'Lengthy', 'Banner', 'Hero', 'Legal'],
    pages: ['Home Page', 'About', 'Contact', 'Banner', 'Privacy', 'Return'],
    showTemplates: false,
    showUploader: false,
    showChangeImage: false,
    showPages: false,
    showOptions: false,

    //Link Variables
    showLink: false,
    showLinkModal: false,
    internalLink: null,
    externalLink: null,
    tags: null,

    //Modal Variables
    showModal: false,
    modalButtons: [
      { title: 'Save', classes: 'outline-primary', emit: 'save' },
      { title: "Don't Save", classes: 'outline-gray', emit: 'leave' },
      { title: 'Cancel', classes: 'outline-gray', emit: 'close' },
    ],
  }),
  components: {
    quillEditor,
    Concise,
    Lengthy,
    Uploader,
    Banner,
    Hero,
    ModalPopup,
    Legal,
    ConfirmationModal,
  },
  computed: {},
  watch: {
    content() {
      this.delta = this.$refs.myQuillEditor.quill.getContents();
    },
    showTemplates(value) {
      if (value) {
        this.showPages = false;
        this.showOptions = false;
        this.showLink = false;
      }
    },
    showPages(value) {
      if (value) {
        this.showTemplates = false;
        this.showOptions = false;
        this.showLink = false;
      }
    },
    showOptions(value) {
      if (value) {
        this.showTemplates = false;
        this.showPages = false;
        this.showLink = false;
      }
    },
    showLink(value) {
      if (value) {
        this.showTemplates = false;
        this.showPages = false;
        this.showOptions = false;
      }
    },
    seeEdit(value) {
      if (value) {
        document.getElementsByTagName('BODY')[0].classList.add('overflow-hidden');
      } else {
        document.getElementsByTagName('BODY')[0].classList.remove('overflow-hidden');
      }
      this.closeHeaders();
    },
  },
  methods: {
    changeLink(value) {
      if (value === 'internal') {
        this.linkTitle = 'Internal';
      } else {
        this.linkTitle = 'External';
      }
      this.showLinkModal = true;
      this.showLink = false;
    },
    setLinkInternal() {
      const link = { name: 'CategoryProduct', query: { type: 'normal' } };
      if (this.internalLink.type === 'Category') {
        link.query.categories = this.internalLink.name;
      } else if (this.internalLink.type === 'Tag') {
        link.name = 'Products';
        link.query.tags = this.internalLink.name;
      } else if (this.internalLink.type === 'Vendor') {
        link.name = 'Products';
        link.query.vendor = this.internalLink.name;
      } else if (this.internalLink.Type === 'Subcategory') {
        link.query.categories = this.internalLink.link.category;
        link.query.subcategories = this.internalLink.link.name;
      } else if (this.internalLink.Type !== 'Category' && this.internalLink.Type !== 'Tag') {
        link.query.categories = this.internalLink.levelOne;
        link.query.subcategories = this.internalLink.name;
      }

      this.layout.link = JSON.stringify(link);
      this.showLinkModal = false;
    },
    setLinkExternal() {
      if (
        this.layout.link.toLowerCase().includes('https://') ||
        this.layout.link.toLowerCase().includes('http://')
      ) {
        this.layout.link = this.externalLink;
        this.showLinkModal = false;
        return;
      }

      this.$store.commit('setError', {
        message: 'External link must begin with either http:// or https://',
        color: 'red',
        duration: 6500,
      });
    },
    changeTemplate(template) {
      this.layout.template = template;
      this.showTemplates = false;

      //Allows you to add link to the layout
      if (template == 'Hero') {
        this.layout.linkEnabled = true;
      } else {
        this.layout.linkEnabled = false;
      }
    },
    async deleteLayout() {
      if (!confirm('Are you sure you want to delete this layout?')) {
        return;
      }
      try {
        await this.$store.dispatch('admin/deleteLayout', this.layout._id);

        this.$store.commit('setError', {
          error: 'Successfully deleted layout',
          color: 'green',
        });
        history.go(-1);
      } catch (error) {
        this.$store.commit('setError', {
          error: error,
          color: 'red',
        });
      }
      this.$emit('close');
    },
    async save() {
      if (this.layout._id != null) {
        this.$store.dispatch('admin/updateLayout', this.layout);
        this.showOptions = false;
      } else {
        this.$store.dispatch('admin/addLayout', this.layout);
      }
    },
    setValues(value) {
      this.layout.imgPath = value;
    },
    back() {
      this.showOptions = false;
      this.$store.dispatch('admin/getLayouts');
      this.$router.go(-1);
    },
    closeHeaders() {
      this.showTemplates = false;
      this.showPages = false;
      this.showOptions = false;
      this.showUploader = false;
      this.showLink = false;
    },
  },
  mounted() {
    this.layout = this.$store.getters.getLayoutByID(this.$route.params.id);

    const tags = this.$store.state.tags.sort((a, b) => {
      if (a.name < b.name) return -1;
      if (a.name > b.name) return 1;
      return 0;
    });
    this.tags = tags;
  },
  created() {
    // let head = document.getElementsByTagName('HEAD')[0];
    // let link = document.createElement('link');
    // link.rel = 'stylesheet';
    // link.type = 'text/css';
    // link.href = 'http://cdn.quilljs.com/1.0.0/quill.snow.css';
    // head.appendChild(link);

    if (this.seeEdit) {
      document.getElementsByTagName('BODY')[0].classList.add('overflow-hidden');
    }
  },
};
</script>

<style>
ul.withIcons {
  min-width: 8rem;
}

ul.noIcons {
  padding: 0.5rem;
  padding-left: 2rem;
  min-width: 8rem;
}
.editLayout {
  background-color: rgba(255, 255, 255, 0.6);
  backdrop-filter: blur(0.5em);
  left: 8rem;
  right: 8rem;
  top: 10vh;
  height: 72vh;
}
.positionModule {
  height: 40vh;
}

/* This is for iPad */
@media only screen and (max-width: 900px) {
  .editLayout {
    left: 4rem;
    right: 4rem;
    top: 10vh;
    height: 70vh;
  }
}

@media only screen and (max-height: 900px) {
  .positionModule {
    height: 35vh;
  }
}
@media only screen and (max-height: 830px) {
  .editLayout {
    height: 80vh;
  }
}
@media only screen and (max-height: 630px) {
  .editLayout {
    height: 86vh;
  }
}

/* This is for mobile */
@media only screen and (max-width: 600px) {
  .editLayout {
    left: 0;
    top: 0;
    height: 100vh;
    width: 100%;
  }

  .positionModule {
    height: 54vh;
  }
  /* sm:left-4 sm:right-4 mx-auto sm:bottom-8 sm:top-6 sm:h-auto sm:w-auto */
}

.shake {
  animation-name: shake;
  animation-duration: 5s;
  animation-delay: 2s;
  animation-iteration-count: infinite;
}

@keyframes shake {
  0%,
  14%,
  100% {
    transform: rotate(0);
  }
  2% {
    transform: rotate(-2deg);
  }
  4% {
    transform: rotate(8deg);
  }
  6% {
    transform: rotate(-8deg);
  }
  8% {
    transform: rotate(4deg);
  }
  10% {
    transform: rotate(-4deg);
  }
  12% {
    transform: rotate(2deg);
  }
}

/* For large screens */
.positionElement {
  width: 24%;
  left: 50%;
  margin-left: -12%;
}

/* This is for mobile */
@media only screen and (max-width: 600px) {
  .positionElement {
  }
}
</style>
