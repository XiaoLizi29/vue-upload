<template>
  <div>
    <el-upload
      drag
      :action="uploadUrl"
      :data="{type:1}"
      :before-upload="beforeUpload"
      :on-exceed="handleExceed"
      multiple
      :style="{ '--width':width ,'--height':height}"
      v-bind="$attrs"
      :on-success="handleSuccess"
      :on-remove="handleRemove"
      :on-preview="handlePreview"
      v-on="$listeners"
    >
      <i slot="default" class="el-icon-plus" />
    </el-upload>

    <!-- 图片预览 -->
    <el-dialog
      :visible.sync="dialog.isShow"
      :close-on-click-modal="false"
      :close-on-press-escape="false"
      append-to-body
    >
      <el-image :src="dialog.url" fit="contain" />
    </el-dialog>
  </div>

</template>

<script>
export default {
  name: 'Upload',
  props: {
    width: {
      type: String,
      default: '200px'
    },
    height: {
      type: String,
      default: '200px'
    }
  },
  data() {
    return {
      uploadUrl: '/ng/system/addFile',
      dialog: {
        url: '',
        isShow: false
      }
    }
  },
  methods: {
    beforeUpload(file) {
      const isPic =
                file.type === 'image/jpeg' || file.type === 'image/png'
      const isLt1M = file.size / 1024 / 1024 < 1

      if (!isPic) {
        this.$message.error('上传图片只能是 jpg或png 格式!')
      } else if (!isLt1M) {
        this.$message.error('上传图片大小不能超过 1MB!')
      }
      return isPic && isLt1M
    },

    handleExceed(files, fileList) {
      this.$message.warning('超出最大上传限制！')
    },

    handleSuccess(response, file, fileList) {
      console.log(fileList)
      const list = fileList.map(item => {
        if (item.response && item.response.returnModel) {
          return item.response.returnModel
        } else {
          return item.url
        }
      })
      console.log(list)
      this.$emit('update:realList', list)
    },

    handleRemove(file, fileList) {
      console.log(fileList)
      const list = fileList.map(item => {
        if (item.response && item.response.returnModel) {
          return item.response.returnModel
        } else {
          return item.url
        }
      })
      console.log(list)
      this.$emit('update:realList', list)
    },

    handlePreview(file) {
      console.log(file)
      this.dialog.url = file.url
      this.dialog.isShow = true
    }
  }
}
</script>

<style lang="scss" scoped>
/deep/ .el-upload-list__item {
    width: var(--width);
    height: var(--height);
}
/deep/ .el-upload--picture-card {
    width: var(--width);
    height: var(--height);
}
/deep/ .el-upload-dragger {
    width: var(--width);
    height: var(--height);
    line-height: var(--height);
}

/deep/.el-dialog__body{
    text-align: center;
}
</style>
