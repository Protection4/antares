<template>
   <ConfirmModal
      :confirm-text="t('word.confirm')"
      size="large"
      class="options-modal"
      :cancel-text="t('word.close')"
      :hide-footer="true"
      @hide="$emit('hide')"
   >
      <template #header>
         <div class="d-flex">
            <i class="mdi mdi-24px mdi-code-tags mr-1" />
            <span class="cut-text">{{ t('word.ddl') }} "{{ table }}"</span>
         </div>
      </template>
      <template #body>
         <div class="pb-4">
            <BaseTextEditor
               ref="queryEditor"
               v-model="createDdl"
               editor-class="textarea-editor"
               :read-only="true"
               mode="sql"
            />
         </div>
      </template>
   </ConfirmModal>
</template>

<script setup lang="ts">
import { onMounted, ref } from 'vue';
import { useNotificationsStore } from '@/stores/notifications';
import { useI18n } from 'vue-i18n';
import Tables from '@/ipc-api/Tables';
import ConfirmModal from '@/components/BaseConfirmModal.vue';
import BaseTextEditor from '@/components/BaseTextEditor.vue';

const { t } = useI18n();

const props = defineProps({
   table: String,
   schema: String,
   workspace: Object
});

const createDdl = ref('');

defineEmits(['hide']);

const { addNotification } = useNotificationsStore();

onMounted(async () => {
   try {
      const { status, response } = await Tables.getTableDll({
         uid: props.workspace.uid,
         table: props.table,
         schema: props.schema
      });

      if (status === 'success')
         createDdl.value = response;
      else
         addNotification({ status: 'error', message: response });
   }
   catch (err) {
      addNotification({ status: 'error', message: err.stack });
   }
});

</script>
