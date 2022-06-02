<template>
  <input
    ref="imageInput"
    type="file"
    accept="image/*"
    style="display: none"
    @change="handleImageUpload">
</template>

<script>
/*!
 * Copyright (c) 2021-2022 Digital Bazaar, Inc. All rights reserved.
 */
import {getCurrentInstance} from 'vue';

export default {
  name: 'BrQResizableImageInput',
  setup() {
    const instance = getCurrentInstance();

    let deferred;
    const getImage = async ({maxImageWidth} = {}) => {
      // transform image upload process (event handlers, etc.) into a promise
      deferred = {
        maxImageWidth,
        cancel() {
          const error = new Error('Image upload canceled.');
          error.name = 'AbortError';
          deferred.reject(error);
        }
      };
      deferred.promise = new Promise((resolve, reject) => {
        deferred.resolve = resolve;
        deferred.reject = reject;
      });
      // if focus returns to the body without an image upload being handled,
      // cancel the image upload
      document.body.addEventListener('focusin', deferred.cancel, {once: true});

      // open image upload dialog
      instance.refs.imageInput.click();
      return deferred.promise;
    };

    const handleImageUpload = async event => {
      // prevent cancelation when focusing back in body again
      document.body.removeEventListener('focusin', deferred.cancel);

      const {target: imageInput} = event;
      const [file] = imageInput.files;
      if(!file) {
        // no file selected, cancel upload
        deferred.cancel();
        return;
      }

      // read image URL
      const src = await _readFile({file});

      // clear input field to trigger `onchange` event next time even if
      // the same image is selected
      imageInput.value = '';

      // resize image
      _resizeImage({src}).then(deferred.resolve, deferred.reject);
    };

    return {handleImageUpload, getImage};
  }
};

function _readFile({file}) {
  return new Promise((resolve, reject) => {
    const reader = new FileReader();
    reader.onload = () => resolve(reader.result);
    reader.onerror = () => reject(reader.error);
    reader.readAsDataURL(file);
  });
}

function _resizeImage({src, maxImageWidth}) {
  return new Promise((resolve, reject) => {
    const image = new Image();
    image.onload = () => resolve(_resize({image, maxImageWidth}));
    image.onerror = () => reject(new Error('Error while loading image.'));
    image.src = src;
  });
}

function _resize({image, maxImageWidth}) {
  // resize the image; ideally done off thread and in steps (if needed to
  // preserve best quality) in the future
  if(maxImageWidth !== undefined) {
    const scale = maxImageWidth / image.width;
    image.width = maxImageWidth;
    image.height = image.height * scale;
  }
  const canvas = document.createElement('canvas');
  canvas.width = image.width;
  canvas.height = image.height;
  const context = canvas.getContext('2d');
  context.drawImage(image, 0, 0, image.width, image.height);

  return {element: canvas, imageUrl: canvas.toDataURL()};
}
</script>

<style lang="scss" scoped>
</style>
