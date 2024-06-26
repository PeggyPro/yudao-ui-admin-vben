<script lang="ts" setup>
import ConfigModal from './ConfigModal.vue'
import { columns } from './config.data'
import { useI18n } from '@/hooks/web/useI18n'
import { useMessage } from '@/hooks/web/useMessage'
import { useModal } from '@/components/Modal'
import { IconEnum } from '@/enums/appEnum'
import { BasicTable, TableAction, useTable } from '@/components/Table'
import { deleteSignInConfig, getSignInConfigList } from '@/api/member/signin/config'
import { DocAlert } from '@/components/DocAlert'

defineOptions({ name: 'SignInConfig' })

const { t } = useI18n()
const { createMessage } = useMessage()
const [registerModal, { openModal }] = useModal()
const [registerTable, { reload }] = useTable({
  api: getSignInConfigList,
  columns,
  showTableSetting: true,
  showIndexColumn: false,
  actionColumn: {
    width: 140,
    title: t('common.action'),
    dataIndex: 'action',
    fixed: 'right',
  },
})

function handleCreate() {
  openModal(true, { isUpdate: false })
}

function handleEdit(record: Recordable) {
  openModal(true, { record, isUpdate: true })
}

async function handleDelete(record: Recordable) {
  await deleteSignInConfig(record.id)
  createMessage.success(t('common.delSuccessText'))
  reload()
}
</script>

<template>
  <div>
    <DocAlert title="会员等级、积分、签到" url="https://doc.iocoder.cn/member/level/" />

    <BasicTable @register="registerTable">
      <template #toolbar>
        <a-button v-auth="['point:sign-in-config:create']" type="primary" :pre-icon="IconEnum.ADD" @click="handleCreate">
          {{ t('action.create') }}
        </a-button>
      </template>
      <template #bodyCell="{ column, record }">
        <template v-if="column.key === 'action'">
          <TableAction
            :actions="[
              { icon: IconEnum.EDIT, label: t('action.edit'), auth: 'point:sign-in-config:update', onClick: handleEdit.bind(null, record) },
              {
                icon: IconEnum.DELETE,
                danger: true,
                label: t('action.delete'),
                auth: 'point:sign-in-config:delete',
                popConfirm: {
                  title: t('common.delMessage'),
                  placement: 'left',
                  confirm: handleDelete.bind(null, record),
                },
              },
            ]"
          />
        </template>
      </template>
    </BasicTable>
    <ConfigModal @register="registerModal" @success="reload()" />
  </div>
</template>
