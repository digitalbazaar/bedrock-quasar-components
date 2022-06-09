<template>
  <input
    ref="imageInput"
    type="file"
    accept="image/*"
    style="display: none"
    @cancel="uploadCanceled"
    @change="handleImageUpload">
</template>

<script>
/*!
 * Copyright (c) 2021-2022 Digital Bazaar, Inc. All rights reserved.
 */
import {getCurrentInstance, onBeforeUnmount} from 'vue';

export default {
  name: 'BrQResizableImageInput',
  setup() {
    const instance = getCurrentInstance();

    let deferred;
    const destroyDeferred = () => deferred && deferred.destroy();
    onBeforeUnmount(destroyDeferred);

    const getImage = async ({maxImageWidth} = {}) => {
      // transform image upload process (event handlers, etc.) into a promise
      destroyDeferred();
      deferred = _createDeferred({maxImageWidth});

      // open image upload dialog
      instance.refs.imageInput.click();
      return deferred.promise;
    };

    const handleImageUpload = async event => {
      // prevent any scheduled cancelation
      deferred.unscheduleCancel();

      const {target: imageInput} = event;
      const [file] = imageInput.files;
      if(!file) {
        // no file selected, cancel upload
        deferred.cancel();
        return;
      }

      // save deferred params and then destroy it
      const {maxImageWidth, resolve, reject} = deferred;
      deferred.destroy();
      deferred = null;

      // read image URL
      const src = await _readFile({file});

      // clear input field to trigger `onchange` event next time even if
      // the same image is selected
      imageInput.value = '';

      // resize image
      _resizeImage({src, maxImageWidth}).then(resolve, reject);
    };

    const uploadCanceled = () => deferred?.cancel();

    return {handleImageUpload, getImage, uploadCanceled};
  }
};

function _createDeferred({maxImageWidth}) {
  let timeoutId;
  const deferred = {
    maxImageWidth,
    unscheduleCancel() {
      clearTimeout(timeoutId);
      document.body.removeEventListener('focusin', deferred.scheduleCancel);
    },
    scheduleCancel() {
      // a timeout of 250ms was arrived at experimentally; 100ms seemed to be
      // sufficient in 100% of tests, but 250ms allows for variance in other
      // browsers -- just deferring to the next turn of the event loop (timeout
      // of `0`) did not work in chromium
      timeoutId = setTimeout(deferred.cancel, 250);
    },
    cancel() {
      const error = new Error('Image upload canceled.');
      error.name = 'AbortError';
      deferred.reject(error);
    },
    destroy() {
      document.body.removeEventListener('focusin', deferred.scheduleCancel);
    }
  };

  deferred.promise = new Promise((resolve, reject) => {
    deferred.resolve = resolve;
    deferred.reject = reject;
  });

  // not every browser has implemented `cancel` event for file dialogs; to
  // approximate behavior the `focusin` event is used; so when focus returns to
  // the body without an image upload being handled, schedule canceling the
  // upload; scheduling is done because `focusin` may be emitted prior to
  // `change` on some versions of some browsers
  document.body.addEventListener(
    'focusin', deferred.scheduleCancel, {once: true});

  return deferred;
}

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
