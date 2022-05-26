<template>
  <div>
    <cld-image :public-id="albumId" class="grow" sign-url="true">
      <cld-transformation width="550" crop="fit" />
      <cld-transformation :overlay="publicid" font-size="80" font-weight="bold" />
      <cld-transformation effect="style_transfer" flags="layer_apply" />
    </cld-image>
  </div>
</template>

<script>
export default {
  props: {
    albumId: { 
      type: String, 
      required: true 
    },
    publicid: { 
      type: String, 
      required: true
    },
  },
}
</script>