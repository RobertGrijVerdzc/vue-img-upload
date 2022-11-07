<template>
  <div
    class="gallery width-100"
    :class="error ? 'red-border' : ''">
    <div class="elements-wraper">
      <div class="button-container image-margin">
        <label
          for="images-upload"
          class="images-upload">
          <svg
            class="custum-icon"
            xmlns="http://www.w3.org/2000/svg"
            width="1em"
            height="1em"
            preserveAspectRatio="xMidYMid meet"
            viewBox="0 0 24 24">
            <g fill="none">
              <path
                d="M12 1C5.925 1 1 5.925 1 12s4.925 11 11 11s11-4.925 11-11S18.075 1 12 1zm1 15a1 1 0 1 1-2 0v-3H8a1 1 0 1 1 0-2h3V8a1 1 0 1 1 2 0v3h3a1 1 0 1 1 0 2h-3v3z"
                fill="currentColor" />
            </g>
          </svg>
        </label>
        <input
          @change="fileChange"
          id="images-upload"
          type="file"
          accept="image/*"
          multiple
          hidden />
      </div>

      <!--IMAGES PREVIEW-->
      <div
        v-for="(image, index) in imgUp"
        :key="index"
        class="image-container image-margin">
        <img
          :src="media_file_path + '/' + image.name"
          alt=""
          class="images-preview" />
        <button
          @click="remove_saved_media(index)"
          class="close-btn"
          type="button">
          <i class="bi bi-x-lg fa-2x"></i>
        </button>
      </div>
    </div>
  </div>
</template>

<script setup>
  import { ref, onMounted, watch } from "vue";
  const axios = require("axios");

  const emmits = defineEmits(["added-media", "saved-media"]);

  const props = defineProps({
    media_server: {
      type: String,
      required: false,
    },
    server: {
      type: String,
      required: false,
    },
    media_file_path: {
      type: String,
      required: false,
      default: "/storage",
    },
    error: {
      type: String,
      required: false,
    },
  });

  watch(
    () => props.media_server,
    (value) => {
      if (value) {
        imgUp.value = [];
        axios.get(value).then((response) => {
          if (response.data.media[0].name != null) {
            imgUp.value = response.data.media;
          } else {
            imgUp.value = [];
          }
          media_emit();
        });
      }
    }
  );

  const imgUp = ref([]);

  const fileChange = async (event) => {
    let files = event.target.files;
    for (var i = 0; i < files.length; i++) {
      let formData = new FormData();
      let url = URL.createObjectURL(files[i]);
      formData.set("image", files[i]);
      const { data } = await axios.post(props.server, formData);
      imgUp.value.push({
        url: url,
        name: data.name,
        size: files[i].size,
        type: files[i].type,
      });
    }
    media_emit();
  };
  const remove_saved_media = (index) => {
    imgUp.value.splice(index, 1);
    media_emit();
  };
  const media_emit = () => {
    emmits("added-media", imgUp.value);
    emmits("saved-media", imgUp.value);
  };

  onMounted(() => {
    if (props.media_server) {
      axios.get(props.media_server).then((response) => {
        if (response.data.media[0].name != null) {
          imgUp.value = response.data.media;
        } else {
          imgUp.value = [];
        }
        media_emit();
      });
    }
  });
</script>

<style scoped>
  .image-wraper {
    min-height: 200px !important;
  }

  .gallery {
    background-color: #fbfbfb !important;
    border-radius: 5px !important;
    border-style: solid !important;
    border: 1px solid #bbbbbb !important;
    height: 85px !important;
    line-height: 1 !important;
    box-sizing: border-box !important;
    height: auto !important;
  }

  .images-upload {
    background-color: #ffffff !important;
    border-radius: 5px !important;
    border: 1px dashed #ccc !important;
    display: inline-block !important;
    cursor: pointer !important;
    width: 165px !important;
    height: 88px !important;
  }
  .images-upload:hover {
    background-color: #f1f1f1 !important;
  }
  .image-container {
    display: inline-table !important;
    height: 90px !important;
    width: 140px !important;
    display: flex !important;
  }
  .images-preview {
    border-radius: 5px !important;
    border: 1px solid #ccc !important;
    display: inline-block !important;
    width: 140px !important;
    height: 88px !important;
    padding-top: -14px !important;
    transition: filter 0.1s linear;
  }
  .images-preview:hover {
    filter: brightness(90%);
  }

  .button-container {
    display: inline-flex !important;
    height: 90px !important;
    width: 140px !important;
    margin-right: 0.25rem !important;
    margin-left: 0.25rem !important;
  }

  .close-btn {
    background: none !important;
    color: red !important;
    border: none !important;
    padding: 0px !important;
    margin: 0px !important;
    font: inherit !important;
    cursor: pointer !important;
    outline: inherit !important;
    position: relative !important;
    right: 34px !important;
    top: -27px !important;
    width: 0px !important;
  }
  .times-icon {
    font-size: 3rem !important;
    padding: 0px !important;
    margin: 0px !important;
  }
  .custum-icon {
    color: #00afca !important;
    font-size: 3rem !important;
    margin-top: 18px !important;
    margin-left: 44px !important;
  }
  .custum-icon:hover {
    color: #29818f !important;
  }
  .close-btn:hover {
    color: rgb(190, 39, 39) !important;
  }

  /* -------------------- */

  .width-100 {
    width: 100% !important;
  }
  .red-border {
    border: 1px solid #dc3545 !important;
    border-color: #dc3545 !important;
  }

  .elements-wraper {
    padding: 1rem !important;
    display: flex !important;
    flex-wrap: wrap !important;
  }
  .align-center {
    text-align: center !important;
  }
  .m-top-1 {
    margin-top: 0.25rem !important;
  }

  .image-margin {
    margin-right: 0.25rem !important;
    margin-left: 0.25rem !important;
    margin-top: 0.25rem !important;
    margin-bottom: 0.25rem !important;
  }
  .red-text {
    color: #d82335;
  }
</style>
