<template>
  <section class="page_operation_module_info">
    <let-form
      inline
      ref="detailForm"
    >
      <let-form-item :label="$t('module.name')" itemWidth="240px" required>
        <let-poptip placement="top" :content="$t('module.namingRule')" trigger="hover">
          <let-input
            size="small"
            v-model="model.module_name"
            :placeholder="$t('module.namingRule')"
            required
            :required-tip="$t('deployService.table.tips.empty')"
            pattern="^[a-zA-Z][a-zA-Z0-9]+$"
            :pattern-tip="$t('module.namingRule')"
          >
          </let-input>
        </let-poptip>
      </let-form-item>
      <!-- <let-form-item :label="$t('module.keyType')" itemWidth="240px" required v-if="module.cache_version == 1">
        <let-select
          size="small"
          v-model="model.key_type"
          required
          :required-tip="$t('deployService.table.tips.empty')"
        >
          <let-option v-for="d in keyTypeOption" :key="d.key" :value="d.key">
            {{d.value}}
          </let-option>
        </let-select>
      </let-form-item> -->
      <let-form-item :label="$t('module.deployArea')" itemWidth="240px">
        <let-select
          size="small"
          v-model="model.idc_area"
          @change="changeSelect"
        >
          <let-option v-for="d in regions" :key="d.label" :value="d.label">
            {{d.region}}
          </let-option>
        </let-select>
      </let-form-item>
      <let-form-item :label="$t('region.setArea')" itemWidth="240px">
        <let-select
          size="small"
          :multiple="true"
          v-model="model.set_area"
        >
          <let-option v-for="d in setRegions" :key="d.label" :value="d.label">
            {{d.region}}
          </let-option>
        </let-select>
      </let-form-item>
      <br>
      <let-form-item :label="$t('cache.perRecordAvg')" itemWidth="240px" required>
        <let-input
          size="small"
          v-model="model.per_record_avg"
          :placeholder="$t('cache.perRecordAvgUnit')"
          required
          :required-tip="$t('deployService.table.tips.empty')"
        >
        </let-input>
      </let-form-item>
      <let-form-item :label="$t('cache.totalRecord')" itemWidth="240px" required>
        <let-input
          size="small"
          v-model="model.total_record"
          :placeholder="$t('cache.dbDataCountUnit')"
          required
          :required-tip="$t('deployService.table.tips.empty')"
        >
        </let-input>
      </let-form-item>
      <let-form-item :label="$t('cache.maxReadFlow')" itemWidth="240px" required>
        <let-input
          size="small"
          v-model="model.max_read_flow"
          :placeholder="$t('cache.flowUnit')"
          required
          :required-tip="$t('deployService.table.tips.empty')"
        >
        </let-input>
      </let-form-item>
      <let-form-item :label="$t('cache.maxWriteFlow')" itemWidth="240px" required>
        <let-input
          size="small"
          v-model="model.max_write_flow"
          :placeholder="$t('cache.flowUnit')"
          required
          :required-tip="$t('deployService.table.tips.empty')"
        >
        </let-input>
      </let-form-item>
      <br>
      <let-form-item :label="$t('module.scenario')" itemWidth="240px" required>
        <let-select
          size="small"
          v-model="model.cache_module_type"
          required
          :required-tip="$t('deployService.table.tips.empty')"
        >
          <let-option v-for="d in cacheModuleType" :key="d.key" :value="d.key">
            {{$t(d.value)}}
          </let-option>
        </let-select>
      </let-form-item>
      <let-form-item :label="$t('cache.cacheType')" itemWidth="500px" v-if="model.cache_module_type == 1" required>
        <let-radio-group size="small" v-model="model.key_type" required
                         :data="cacheTypeOption">
        </let-radio-group>
      </let-form-item>
      <let-form-item :label="$t('cache.dbAccessServantObj')" itemWidth="240px" v-if="model.cache_module_type == 2"
                     required>
        <let-input
          size="small"
          v-model="model.dbAccessServant"
          :placeholder="$t('cache.dbAccessServantObjEx')"
          required
          :required-tip="$t('deployService.table.tips.empty')"
        >
        </let-input>
      </let-form-item>
      <br>
      <let-form-item :label="$t('cache.moduleRemark')" itemWidth="516px" required>
        <let-input
          size="small"
          v-model="model.module_remark"
          type="textarea"
          :rows="4"
          required
          :required-tip="$t('deployService.table.tips.empty')"
        >
        </let-input>
      </let-form-item>
      <div>
        <let-button size="small" theme="primary" @click="addModuleConfig">{{$t('common.nextStep')}}</let-button>
      </div>
    </let-form>
  </section>
</template>

<script>
  const getInitialModel = () => ({
    admin: '',
    idc_area: '',
    key_type: null,
    module_name: '',    // 模块名
    cache_module_type: '',  // 模块存储类型
    key_type: '',   // 存储类型
    dbAccessServant: '',  // 数据库servant obj
    per_record_avg: '',
    total_record: '',
    max_read_flow: '',
    max_write_flow: '',
    module_remark: '',
    set_area: [],
    module_id: '',
    apply_id: '',
  });
  const cacheModuleType = [
    { key: '1', value: 'cache.title' },
    { key: '2', value: 'cache.cachePersistent' },
  ];
  const keyTypeOption = [
    { key: '0', value: 'string' },
    { key: '1', value: 'int' },
    { key: '2', value: 'longlong' },
  ];
  import Ajax from '@/plugins/ajax'

  export default {
    data() {
      const cacheTypeOption = [
        { value: '1', text: this.$t('cache.cacheTypeTip1') },
        { value: '2', text: this.$t('cache.cacheTypeTip2') },
        { value: '3', text: this.$t('cache.cacheTypeTip3') },
      ];
      return {
        cacheTypeOption,
        keyTypeOption,
        cacheModuleType,
        regions: [],
        setRegions: [],
        module: {
          cache_version: 1,
        },
        app_name: '',
        model: getInitialModel()
      }
    },
    methods: {
      addModuleConfig() {
        const regex = new RegExp('^' + this.app_name + '[a-zA-Z0-9]+$');
        if (!regex.test(this.model.module_name)) {
          this.$tip.error(this.$t('module.namingError'));
        } else if (this.$refs.detailForm.validate()) {
          const model = this.model;
          const url = '/server/api/add_module_config';
          const loading = this.$Loading.show();
          this.$ajax.postJSON(url, model).then((data) => {
            loading.hide();
            this.$tip.success(this.$t('common.success'));
            this.$router.push('/operation/module/serverConfig/' + data.module_id)
          }).catch((err) => {
            loading.hide();
            this.$tip.error(`${this.$t('common.error')}: ${err.message || err.err_msg}`);
          });
        }
      },
      changeSelect() {
        this.model.set_area = [];
        let setRegions = this.regions.concat();
        setRegions.splice(setRegions.indexOf(setRegions.find(item => item.label == this.model.idc_area)), 1);
        this.setRegions = setRegions;
      },
      getRegionList() {
        this.$ajax.getJSON('/server/api/get_region_list').then((regions) => {
          if (regions.length) {
            this.regions = regions
          } else {
            // this.$tip.warning(`${vm.$t('common.warning')}: ${vm.$t('apply.createRegionTips')}`);
            // this.$router.push('region')
          }
        }).catch((err) => {
          this.$tip.error(`${this.$t('common.error')}: ${err.message || err.err_msg}`);
        });
      },
      getModuleInfo() {
        let { moduleId } = this.$route.params;
        this.$ajax.getJSON('/server/api/get_module_info', { moduleId }).then((data) => {
          this.model.apply_id = data.apply_id;
          this.model.module_id = data.id;
          this.module = data;
          this.getAppName(data.apply_id);
        }).catch((err) => {
          this.$tip.error(`${this.$t('common.error')}: ${err.message || err.err_msg}`);
        });
      },
      getAppName(applyId) {
        this.$ajax.getJSON('/server/api/get_apply_and_router_and_proxy', { applyId }).then((apply) => {
          this.app_name = apply.name || '';
          this.model.module_name = this.app_name;
        }).catch((err) => {
          this.$tip.error(`${this.$t('common.error')}: ${err.message || err.err_msg}`);
        });
      }
    },
    created() {
      this.getRegionList();
      this.getModuleInfo();
    }
  }
</script>

<style lang="postcss">
  .let-form-cols-1 .let-form-item__label {
    width: auto;
  }
  .page_operation_module_info {
    .poptip__popper {
      max-width: 420px;
    }

    .let-poptip {
      display: block;
    }

    .let-poptip__trigger {
      display: block;
    }

    .let-poptip_top {
      width: auto !important;
      padding: 4px 8px;
      background: #f56c77;
      color: white;
      border-radius: 6px;
      top: -25px !important;

      .let-poptip__arrow:after {
        border-top-color: #f56c77;
      }

      .let-poptip__content {
        font-size: 14px;
        white-space: nowrap;
      }
    }
  }
</style>
