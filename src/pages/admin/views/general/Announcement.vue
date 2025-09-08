<template>
  <div class="announcement view">
    <Panel :title="$t('m.General_Announcement')">
      <div class="list">
        <el-table
          v-loading="loading"
          element-loading-text="Đang tải..."
          ref="table"
          :data="announcementList"
          style="width: 100%">
          <el-table-column
            width="100"
            prop="id"
            label="Mã">
          </el-table-column>
          <el-table-column
            prop="title"
            label="Tiêu đề">
          </el-table-column>
          <el-table-column
            prop="create_time"
            label="Ngày tạo">
            <template slot-scope="scope">
              {{ scope.row.create_time | localtime }}
            </template>
          </el-table-column>
          <el-table-column
            prop="last_update_time"
            label="Cập nhật lần cuối">
            <template slot-scope="scope">
              {{ scope.row.last_update_time | localtime }}
            </template>
          </el-table-column>
          <el-table-column
            prop="created_by.username"
            label="Tác giả">
          </el-table-column>
          <el-table-column
            width="100"
            prop="visible"
            label="Hiển thị">
            <template slot-scope="scope">
              <el-switch v-model="scope.row.visible"
                         active-text=""
                         inactive-text=""
                         @change="handleVisibleSwitch(scope.row)">
              </el-switch>
            </template>
          </el-table-column>
          <el-table-column
            fixed="right"
            label="Tùy chọn"
            width="200">
            <div slot-scope="scope">
              <icon-btn name="Sửa" icon="edit" @click.native="openAnnouncementDialog(scope.row.id)"></icon-btn>
              <icon-btn name="Xoá" icon="trash" @click.native="deleteAnnouncement(scope.row.id)"></icon-btn>
            </div>
          </el-table-column>
        </el-table>
        <div class="panel-options">
          <el-button type="primary" size="small" @click="openAnnouncementDialog(null)" icon="el-icon-plus">Tạo mới</el-button>
          <el-pagination
            v-if="!contestID"
            class="page"
            layout="prev, pager, next"
            @current-change="currentChange"
            :page-size="pageSize"
            :total="total">
          </el-pagination>
        </div>
      </div>
    </Panel>
    <!-- Hộp thoại -->
    <el-dialog :title="announcementDialogTitle" :visible.sync="showEditAnnouncementDialog"
               @open="onOpenEditDialog" :close-on-click-modal="false">
      <el-form label-position="top">
        <el-form-item :label="$t('m.Announcement_Title')" required>
          <el-input
            v-model="announcement.title"
            :placeholder="$t('m.Announcement_Title')" class="title-input">
          </el-input>
        </el-form-item>
        <el-form-item :label="$t('m.Announcement_Content')" required>
          <Simditor v-model="announcement.content"></Simditor>
        </el-form-item>
        <div class="visible-box">
          <span>{{$t('m.Announcement_visible')}}</span>
          <el-switch
            v-model="announcement.visible"
            active-text=""
            inactive-text="">
          </el-switch>
        </div>
      </el-form>
      <span slot="footer" class="dialog-footer">
          <cancel @click.native="showEditAnnouncementDialog = false">Hủy</cancel>
          <save type="primary" @click.native="submitAnnouncement">Lưu</save>
        </span>
    </el-dialog>
  </div>
</template>

<script>
  import Simditor from '../../components/Simditor.vue'
  import api from '../../api.js'

  export default {
    name: 'Announcement',
    components: {
      Simditor
    },
    data () {
      return {
        contestID: '',
        // Hiển thị hộp thoại sửa/tạo thông báo
        showEditAnnouncementDialog: false,
        // Danh sách thông báo
        announcementList: [],
        // Số thông báo mỗi trang
        pageSize: 15,
        // Tổng số thông báo
        total: 0,
        // ID thông báo hiện tại
        currentAnnouncementId: null,
        mode: 'create',
        // Model thông báo (tạo | sửa)
        announcement: {
          title: '',
          visible: true,
          content: ''
        },
        // Tiêu đề hộp thoại
        announcementDialogTitle: 'Sửa thông báo',
        // Trạng thái loading
        loading: true,
        // Trang hiện tại
        currentPage: 0
      }
    },
    mounted () {
      this.init()
    },
    methods: {
      init () {
        this.contestID = this.$route.params.contestId
        if (this.contestID) {
          this.getContestAnnouncementList()
        } else {
          this.getAnnouncementList(1)
        }
      },
      // Callback đổi trang
      currentChange (page) {
        this.currentPage = page
        this.getAnnouncementList(page)
      },
      getAnnouncementList (page) {
        this.loading = true
        api.getAnnouncementList((page - 1) * this.pageSize, this.pageSize).then(res => {
          this.loading = false
          this.total = res.data.data.total
          this.announcementList = res.data.data.results
        }, res => {
          this.loading = false
        })
      },
      getContestAnnouncementList () {
        this.loading = true
        api.getContestAnnouncementList(this.contestID).then(res => {
          this.loading = false
          this.announcementList = res.data.data
        }).catch(() => {
          this.loading = false
        })
      },
      // Mở hộp thoại chỉnh sửa
      onOpenEditDialog () {
        // Tạm thời sửa lỗi hiển thị editor
        setTimeout(() => {
          if (document.createEvent) {
            let event = document.createEvent('HTMLEvents')
            event.initEvent('resize', true, true)
            window.dispatchEvent(event)
          } else if (document.createEventObject) {
            window.fireEvent('onresize')
          }
        }, 0)
      },
      // Gửi (tạo/sửa) thông báo
      // Mặc định nhận MouseEvent
      submitAnnouncement (data = undefined) {
        let funcName = ''
        if (!data || !data.title) {
          data = {
            id: this.currentAnnouncementId,
            title: this.announcement.title,
            content: this.announcement.content,
            visible: this.announcement.visible
          }
        }
        if (this.contestID) {
          data.contest_id = this.contestID
          funcName = this.mode === 'edit' ? 'updateContestAnnouncement' : 'createContestAnnouncement'
        } else {
          funcName = this.mode === 'edit' ? 'updateAnnouncement' : 'createAnnouncement'
        }
        api[funcName](data).then(res => {
          this.showEditAnnouncementDialog = false
          this.init()
        }).catch(() => {})
      },
      // Xoá thông báo
      deleteAnnouncement (announcementId) {
        this.$confirm('Bạn có chắc muốn xoá thông báo này?', 'Cảnh báo', {
          confirmButtonText: 'Xoá',
          cancelButtonText: 'Hủy',
          type: 'warning'
        }).then(() => {
          // xác nhận
          this.loading = true
          let funcName = this.contestID ? 'deleteContestAnnouncement' : 'deleteAnnouncement'
          api[funcName](announcementId).then(res => {
            this.loading = true
            this.init()
          })
        }).catch(() => {
          // huỷ
          this.loading = false
        })
      },
      openAnnouncementDialog (id) {
        this.showEditAnnouncementDialog = true
        if (id !== null) {
          this.currentAnnouncementId = id
          this.announcementDialogTitle = 'Sửa thông báo'
          this.announcementList.find(item => {
            if (item.id === this.currentAnnouncementId) {
              this.announcement.title = item.title
              this.announcement.visible = item.visible
              this.announcement.content = item.content
              this.mode = 'edit'
            }
          })
        } else {
          this.announcementDialogTitle = 'Tạo thông báo'
          this.announcement.title = ''
          this.announcement.visible = true
          this.announcement.content = ''
          this.mode = 'create'
        }
      },
      handleVisibleSwitch (row) {
        this.mode = 'edit'
        this.submitAnnouncement({
          id: row.id,
          title: row.title,
          content: row.content,
          visible: row.visible
        })
      }
    },
    watch: {
      $route () {
        this.init()
      }
    }
  }
</script>

<style lang="less" scoped>
  .title-input {
    margin-bottom: 20px;
  }

  .visible-box {
    margin-top: 10px;
    width: 205px;
    float: left;
  }
</style>
