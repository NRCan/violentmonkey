<template>
</template>

<script>
import Tooltip from 'vueleton/lib/tooltip';
import { sendCmdDirectly } from '@/common';
import options from '@/common/options';
import SettingCheck from '@/common/ui/setting-check';
import hookSetting from '@/common/hook-setting';
import Icon from '@/common/ui/icon';
import { store } from '../../utils';

const SYNC_CURRENT = 'sync.current';
const syncConfig = {
  current: '',
};
hookSetting(SYNC_CURRENT, (value) => {
  syncConfig.current = value || '';
});

export default {
  components: {
    SettingCheck,
    Icon,
    Tooltip,
  },
  data() {
    return {
      syncConfig,
      store,
    };
  },
  computed: {
    syncServices() {
      const states = this.store.sync;
      if (states && states.length) {
        return [
          {
            displayName: this.i18n('labelSyncDisabled'),
            name: '',
            properties: {},
          },
          ...states,
        ];
      }
      return null;
    },
    service() {
      if (this.syncServices) {
        const current = this.syncConfig.current || '';
        let service = this.syncServices.find(item => item.name === current);
        if (!service) {
          console.warn('Invalid current service:', current);
          service = this.syncServices[0];
        }
        return service;
      }
      return null;
    },
    state() {
      const { service } = this;
      if (service) {
        const canAuthorize = ['idle', 'error'].includes(service.syncState)
          && ['no-auth', 'unauthorized', 'error', 'authorized'].includes(service.authState);
        const canSync = canAuthorize && service.authState === 'authorized';
        return {
          message: this.getMessage(),
          label: this.getLabel(),
          canAuthorize,
          canSync,
          authType: service.properties.authType,
          userConfig: service.userConfig || {},
        };
      }
      return null;
    },
  },
  methods: {
    onSaveUserConfig() {
      sendCmdDirectly('SyncSetConfig', this.state.userConfig);
    },
    onSyncChange(e) {
      const { value } = e.target;
      options.set(SYNC_CURRENT, value);
    },
    onAuthorize() {
      const { service } = this;
      if (['authorized'].includes(service.authState)) {
        // revoke
        sendCmdDirectly('SyncRevoke');
      } else if (['no-auth', 'unauthorized', 'error'].includes(service.authState)) {
        // authorize
        sendCmdDirectly('SyncAuthorize');
      }
    },
    onSync() {
      sendCmdDirectly('SyncStart');
    },
    getMessage() {
      const { service } = this;
      if (service.authState === 'initializing') return this.i18n('msgSyncInit');
      if (service.authState === 'no-auth') return this.i18n('msgSyncNoAuthYet');
      if (service.authState === 'error') return this.i18n('msgSyncInitError');
      if (service.authState === 'unauthorized') return this.i18n('msgSyncInitError');
      if (service.syncState === 'error') return this.i18n('msgSyncError');
      if (service.syncState === 'ready') return this.i18n('msgSyncReady');
      if (service.syncState === 'syncing') {
        let progress = '';
        if (service.progress && service.progress.total) {
          progress = ` (${service.progress.finished}/${service.progress.total})`;
        }
        return this.i18n('msgSyncing') + progress;
      }
      if (service.lastSync) {
        const lastSync = new Date(service.lastSync).toLocaleString();
        return this.i18n('lastSync', lastSync);
      }
    },
    getLabel() {
      const { service } = this;
      if (service.authState === 'authorizing') return this.i18n('labelSyncAuthorizing');
      if (service.authState === 'authorized') return this.i18n('labelSyncRevoke');
      return this.i18n('labelSyncAuthorize');
    },
  },
};
</script>

<style>
.sync-server-url {
  > input {
    width: 400px;
  }
}
</style>
