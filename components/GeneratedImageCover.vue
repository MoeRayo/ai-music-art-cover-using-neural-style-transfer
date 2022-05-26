
<template>
  <div>
  
    <cld-image ref="ref" name="cat.png" :public-id="publicId">
      <cld-transformation effect="blur:150"/>
      <cld-transformation effect="brightness_hsb:-20"/>
      <cld-transformation :overlay="{fontFamily: 'Metamorphous', fontSize: 80, fontWeight: 'bold', text: text, testAlign: 'center'}" color="#fff" opacity="80" crop="fit" width="550" effect="shadow"/>
      <cld-transformation flags="layer_apply"/>
    </cld-image>
    
  </div>
</template>
<script>
export default {
  props: {
    text: { 
      type: String, 
      required: true 
    },
    publicId: { 
      type: String, 
      required: true
    },
  },
}
</script>