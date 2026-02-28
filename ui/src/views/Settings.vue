<!--
  Copyright (C) 2022 Nethesis S.r.l.
  SPDX-License-Identifier: GPL-3.0-or-later
-->
<template>
  <cv-grid fullWidth>
    <cv-row>
      <cv-column class="page-title">
        <h2>{{ $t("settings.title") }}</h2>
      </cv-column>
    </cv-row>
    <cv-row v-if="error.getConfiguration">
      <cv-column>
        <NsInlineNotification
          kind="error"
          :title="$t('action.get-configuration')"
          :description="error.getConfiguration"
          :showCloseButton="false"
        />
      </cv-column>
    </cv-row>
    <cv-row>
      <cv-column>
        <cv-tile light>
          <cv-form @submit.prevent="configureModule">
            <cv-text-input
              :label="$t('settings.docuseal_fqdn')"
              placeholder="docuseal.example.org"
              v-model.trim="host"
              class="mg-bottom"
              :invalid-message="$t(error.host)"
              :disabled="loading.getConfiguration || loading.configureModule"
              ref="host"
            >
            </cv-text-input>
            <cv-toggle
              value="letsEncrypt"
              :label="$t('settings.lets_encrypt')"
              v-model="isLetsEncryptEnabled"
              :disabled="loading.getConfiguration || loading.configureModule"
              class="mg-bottom"
            >
              <template slot="text-left">{{
                $t("settings.disabled")
              }}</template>
              <template slot="text-right">{{
                $t("settings.enabled")
              }}</template>
            </cv-toggle>
            <cv-toggle
              value="httpToHttps"
              :label="$t('settings.http_to_https')"
              v-model="isHttpToHttpsEnabled"
              :disabled="loading.getConfiguration || loading.configureModule"
              class="mg-bottom"
            >
              <template slot="text-left">{{
                $t("settings.disabled")
              }}</template>
              <template slot="text-right">{{
                $t("settings.enabled")
              }}</template>
            </cv-toggle>
            <!-- advanced options -->
            <cv-accordion ref="accordion" class="maxwidth mg-bottom">
              <cv-accordion-item :open="toggleAccordion[0]">
                <template slot="title">{{ $t("settings.advanced") }}</template>
                <template slot="content">
                  <cv-text-input
                    :label="$t('settings.web_concurrency')"
                    v-model.number="webConcurrency"
                    type="number"
                    min="1"
                    class="mg-bottom"
                    :disabled="
                      loading.getConfiguration || loading.configureModule
                    "
                  >
                  </cv-text-input>
                  <cv-text-input
                    :label="$t('settings.session_remember_days')"
                    v-model.number="sessionRememberDays"
                    type="number"
                    min="1"
                    class="mg-bottom"
                    :disabled="
                      loading.getConfiguration || loading.configureModule
                    "
                  >
                  </cv-text-input>
                  <cv-text-input
                    :label="$t('settings.presigned_urls_expire_minutes')"
                    v-model.number="presignedUrlsExpireMinutes"
                    type="number"
                    min="1"
                    class="mg-bottom"
                    :disabled="
                      loading.getConfiguration || loading.configureModule
                    "
                  >
                  </cv-text-input>
                  <cv-text-input
                    :label="$t('settings.stripe_secret_key')"
                    v-model="stripeSecretKey"
                    type="password"
                    class="mg-bottom"
                    :disabled="
                      loading.getConfiguration || loading.configureModule
                    "
                  >
                  </cv-text-input>
                  <cv-text-input
                    :label="$t('settings.gotenberg_url')"
                    v-model="gotenbergUrl"
                    placeholder="http://gotenberg:3000"
                    class="mg-bottom"
                    :disabled="
                      loading.getConfiguration || loading.configureModule
                    "
                  >
                  </cv-text-input>
                </template>
              </cv-accordion-item>
              <cv-accordion-item :open="toggleAccordion[1]">
                <template slot="title">{{
                  $t("settings.smtp_configuration")
                }}</template>
                <template slot="content">
                  <cv-text-input
                    :label="$t('settings.smtp_address')"
                    v-model="smtpAddress"
                    class="mg-bottom"
                    :disabled="
                      loading.getConfiguration || loading.configureModule
                    "
                  >
                  </cv-text-input>
                  <cv-text-input
                    :label="$t('settings.smtp_port')"
                    v-model="smtpPort"
                    class="mg-bottom"
                    :disabled="
                      loading.getConfiguration || loading.configureModule
                    "
                  >
                  </cv-text-input>
                  <cv-text-input
                    :label="$t('settings.smtp_username')"
                    v-model="smtpUsername"
                    class="mg-bottom"
                    :disabled="
                      loading.getConfiguration || loading.configureModule
                    "
                  >
                  </cv-text-input>
                  <cv-text-input
                    :label="$t('settings.smtp_password')"
                    v-model="smtpPassword"
                    type="password"
                    class="mg-bottom"
                    :disabled="
                      loading.getConfiguration || loading.configureModule
                    "
                  >
                  </cv-text-input>
                  <cv-text-input
                    :label="$t('settings.smtp_domain')"
                    v-model="smtpDomain"
                    class="mg-bottom"
                    :disabled="
                      loading.getConfiguration || loading.configureModule
                    "
                  >
                  </cv-text-input>
                  <cv-text-input
                    :label="$t('settings.smtp_from')"
                    v-model="smtpFrom"
                    class="mg-bottom"
                    :disabled="
                      loading.getConfiguration || loading.configureModule
                    "
                  >
                  </cv-text-input>
                  <cv-select
                    :label="$t('settings.smtp_authentication')"
                    v-model="smtpAuthentication"
                    class="mg-bottom"
                    :disabled="
                      loading.getConfiguration || loading.configureModule
                    "
                    :options="smtpAuthenticationOptions"
                  >
                  </cv-select>
                  <cv-toggle
                    :label="$t('settings.smtp_enable_starttls')"
                    v-model="smtpEnableStarttls"
                    :disabled="
                      loading.getConfiguration || loading.configureModule
                    "
                    class="mg-bottom"
                  >
                    <template slot="text-left">{{
                      $t("settings.disabled")
                    }}</template>
                    <template slot="text-right">{{
                      $t("settings.enabled")
                    }}</template>
                  </cv-toggle>
                  <cv-toggle
                    :label="$t('settings.smtp_ssl_verify')"
                    v-model="smtpSslVerify"
                    :disabled="
                      loading.getConfiguration || loading.configureModule
                    "
                    class="mg-bottom"
                  >
                    <template slot="text-left">{{
                      $t("settings.disabled")
                    }}</template>
                    <template slot="text-right">{{
                      $t("settings.enabled")
                    }}</template>
                  </cv-toggle>
                </template>
              </cv-accordion-item>
              <cv-accordion-item :open="toggleAccordion[2]">
                <template slot="title">{{
                  $t("settings.aws_s3_configuration")
                }}</template>
                <template slot="content">
                  <cv-text-input
                    :label="$t('settings.aws_access_key_id')"
                    v-model="awsAccessKeyId"
                    class="mg-bottom"
                    :disabled="
                      loading.getConfiguration || loading.configureModule
                    "
                  >
                  </cv-text-input>
                  <cv-text-input
                    :label="$t('settings.aws_secret_access_key')"
                    v-model="awsSecretAccessKey"
                    type="password"
                    class="mg-bottom"
                    :disabled="
                      loading.getConfiguration || loading.configureModule
                    "
                  >
                  </cv-text-input>
                  <cv-text-input
                    :label="$t('settings.aws_region')"
                    v-model="awsRegion"
                    placeholder="us-east-1"
                    class="mg-bottom"
                    :disabled="
                      loading.getConfiguration || loading.configureModule
                    "
                  >
                  </cv-text-input>
                  <cv-text-input
                    :label="$t('settings.s3_attachments_bucket')"
                    v-model="s3AttachmentsBucket"
                    class="mg-bottom"
                    :disabled="
                      loading.getConfiguration || loading.configureModule
                    "
                  >
                  </cv-text-input>
                </template>
              </cv-accordion-item>
              <cv-accordion-item :open="toggleAccordion[3]">
                <template slot="title">{{
                  $t("settings.gcp_storage_configuration")
                }}</template>
                <template slot="content">
                  <cv-text-input
                    :label="$t('settings.gcs_credentials')"
                    v-model="gcsCredentials"
                    type="password"
                    class="mg-bottom"
                    :disabled="
                      loading.getConfiguration || loading.configureModule
                    "
                  >
                  </cv-text-input>
                  <cv-text-input
                    :label="$t('settings.gcs_project')"
                    v-model="gcsProject"
                    class="mg-bottom"
                    :disabled="
                      loading.getConfiguration || loading.configureModule
                    "
                  >
                  </cv-text-input>
                  <cv-text-input
                    :label="$t('settings.gcs_bucket')"
                    v-model="gcsBucket"
                    class="mg-bottom"
                    :disabled="
                      loading.getConfiguration || loading.configureModule
                    "
                  >
                  </cv-text-input>
                </template>
              </cv-accordion-item>
              <cv-accordion-item :open="toggleAccordion[4]">
                <template slot="title">{{
                  $t("settings.azure_cloud_configuration")
                }}</template>
                <template slot="content">
                  <cv-text-input
                    :label="$t('settings.azure_storage_account_name')"
                    v-model="azureStorageAccountName"
                    class="mg-bottom"
                    :disabled="
                      loading.getConfiguration || loading.configureModule
                    "
                  >
                  </cv-text-input>
                  <cv-text-input
                    :label="$t('settings.azure_storage_access_key')"
                    v-model="azureStorageAccessKey"
                    type="password"
                    class="mg-bottom"
                    :disabled="
                      loading.getConfiguration || loading.configureModule
                    "
                  >
                  </cv-text-input>
                  <cv-text-input
                    :label="$t('settings.azure_container')"
                    v-model="azureContainer"
                    class="mg-bottom"
                    :disabled="
                      loading.getConfiguration || loading.configureModule
                    "
                  >
                  </cv-text-input>
                </template>
              </cv-accordion-item>
            </cv-accordion>
            <cv-row v-if="error.configureModule">
              <cv-column>
                <NsInlineNotification
                  kind="error"
                  :title="$t('action.configure-module')"
                  :description="error.configureModule"
                  :showCloseButton="false"
                />
              </cv-column>
            </cv-row>
            <NsButton
              kind="primary"
              :icon="Save20"
              :loading="loading.configureModule"
              :disabled="loading.getConfiguration || loading.configureModule"
              >{{ $t("settings.save") }}</NsButton
            >
          </cv-form>
        </cv-tile>
      </cv-column>
    </cv-row>
  </cv-grid>
</template>

<script>
import to from "await-to-js";
import { mapState } from "vuex";
import {
  QueryParamService,
  UtilService,
  TaskService,
  IconService,
  PageTitleService,
} from "@nethserver/ns8-ui-lib";

export default {
  name: "Settings",
  mixins: [
    TaskService,
    IconService,
    UtilService,
    QueryParamService,
    PageTitleService,
  ],
  pageTitle() {
    return this.$t("settings.title") + " - " + this.appName;
  },
  data() {
    return {
      q: {
        page: "settings",
      },
      urlCheckInterval: null,
      toggleAccordion: [false, false, false, false, false],
      host: "",
      isLetsEncryptEnabled: false,
      isHttpToHttpsEnabled: true,
      webConcurrency: 1,
      sessionRememberDays: 730,
      presignedUrlsExpireMinutes: 240,
      stripeSecretKey: "",
      gotenbergUrl: "",
      smtpAddress: "",
      smtpPort: "",
      smtpUsername: "",
      smtpPassword: "",
      smtpDomain: "",
      smtpFrom: "",
      smtpAuthentication: "plain",
      smtpAuthenticationOptions: [
        { value: "plain", text: "plain" },
        { value: "login", text: "login" },
      ],
      smtpEnableStarttls: true,
      smtpSslVerify: true,
      awsAccessKeyId: "",
      awsSecretAccessKey: "",
      awsRegion: "",
      s3AttachmentsBucket: "",
      gcsCredentials: "",
      gcsProject: "",
      gcsBucket: "",
      azureStorageAccountName: "",
      azureStorageAccessKey: "",
      azureContainer: "",
      loading: {
        getConfiguration: false,
        configureModule: false,
      },
      error: {
        getConfiguration: "",
        configureModule: "",
        host: "",
        lets_encrypt: "",
        http2https: "",
      },
    };
  },
  computed: {
    ...mapState(["instanceName", "core", "appName"]),
  },
  created() {
    this.getConfiguration();
  },
  beforeRouteEnter(to, from, next) {
    next((vm) => {
      vm.watchQueryData(vm);
      vm.urlCheckInterval = vm.initUrlBindingForApp(vm, vm.q.page);
    });
  },
  beforeRouteLeave(to, from, next) {
    clearInterval(this.urlCheckInterval);
    next();
  },
  methods: {
    async getConfiguration() {
      this.loading.getConfiguration = true;
      this.error.getConfiguration = "";
      const taskAction = "get-configuration";
      const eventId = this.getUuid();

      // register to task error
      this.core.$root.$once(
        `${taskAction}-aborted-${eventId}`,
        this.getConfigurationAborted,
      );

      // register to task completion
      this.core.$root.$once(
        `${taskAction}-completed-${eventId}`,
        this.getConfigurationCompleted,
      );

      const res = await to(
        this.createModuleTaskForApp(this.instanceName, {
          action: taskAction,
          extra: {
            title: this.$t("action." + taskAction),
            isNotificationHidden: true,
            eventId,
          },
        }),
      );
      const err = res[0];

      if (err) {
        console.error(`error creating task ${taskAction}`, err);
        this.error.getConfiguration = this.getErrorMessage(err);
        this.loading.getConfiguration = false;
        return;
      }
    },
    getConfigurationAborted(taskResult, taskContext) {
      console.error(`${taskContext.action} aborted`, taskResult);
      this.error.getConfiguration = this.$t("error.generic_error");
      this.loading.getConfiguration = false;
    },
    getConfigurationCompleted(taskContext, taskResult) {
      const config = taskResult.output;
      this.host = config.host;
      this.isLetsEncryptEnabled = config.lets_encrypt;
      this.isHttpToHttpsEnabled = config.http2https;
      this.webConcurrency = config.web_concurrency;
      this.sessionRememberDays = config.session_remember_days;
      this.presignedUrlsExpireMinutes = config.presigned_urls_expire_minutes;
      this.stripeSecretKey = config.stripe_secret_key;
      this.gotenbergUrl = config.gotenberg_url;
      this.smtpAddress = config.smtp_address || "";
      this.smtpPort = config.smtp_port || "";
      this.smtpUsername = config.smtp_username || "";
      this.smtpPassword = "";
      this.smtpDomain = config.smtp_domain || "";
      this.smtpFrom = config.smtp_from || "";
      this.smtpAuthentication = config.smtp_authentication || "plain";
      this.smtpEnableStarttls = config.smtp_enable_starttls;
      this.smtpSslVerify = config.smtp_ssl_verify;
      this.awsAccessKeyId = config.aws_access_key_id;
      this.awsSecretAccessKey = config.aws_secret_access_key;
      this.awsRegion = config.aws_region;
      this.s3AttachmentsBucket = config.s3_attachments_bucket;
      this.gcsCredentials = config.gcs_credentials;
      this.gcsProject = config.gcs_project;
      this.gcsBucket = config.gcs_bucket;
      this.azureStorageAccountName = config.azure_storage_account_name;
      this.azureStorageAccessKey = config.azure_storage_access_key;
      this.azureContainer = config.azure_container;

      this.loading.getConfiguration = false;
      this.focusElement("host");
    },
    validateConfigureModule() {
      this.clearErrors(this);

      let isValidationOk = true;
      if (!this.host) {
        this.error.host = "common.required";

        if (isValidationOk) {
          this.focusElement("host");
        }
        isValidationOk = false;
      }
      return isValidationOk;
    },
    configureModuleValidationFailed(validationErrors) {
      this.loading.configureModule = false;
      let focusAlreadySet = false;

      for (const validationError of validationErrors) {
        const param = validationError.parameter;
        // set i18n error message
        this.error[param] = this.$t("settings." + validationError.error);

        if (!focusAlreadySet) {
          this.focusElement(param);
          focusAlreadySet = true;
        }
      }
    },
    async configureModule() {
      this.error.test_imap = false;
      this.error.test_smtp = false;
      const isValidationOk = this.validateConfigureModule();
      if (!isValidationOk) {
        return;
      }

      this.loading.configureModule = true;
      const taskAction = "configure-module";
      const eventId = this.getUuid();

      // register to task error
      this.core.$root.$once(
        `${taskAction}-aborted-${eventId}`,
        this.configureModuleAborted,
      );

      // register to task validation
      this.core.$root.$once(
        `${taskAction}-validation-failed-${eventId}`,
        this.configureModuleValidationFailed,
      );

      // register to task completion
      this.core.$root.$once(
        `${taskAction}-completed-${eventId}`,
        this.configureModuleCompleted,
      );
      const res = await to(
        this.createModuleTaskForApp(this.instanceName, {
          action: taskAction,
          data: {
            host: this.host,
            lets_encrypt: this.isLetsEncryptEnabled,
            http2https: this.isHttpToHttpsEnabled,
            web_concurrency: this.webConcurrency,
            session_remember_days: this.sessionRememberDays,
            presigned_urls_expire_minutes: this.presignedUrlsExpireMinutes,
            stripe_secret_key: this.stripeSecretKey,
            gotenberg_url: this.gotenbergUrl,
            smtp_address: this.smtpAddress,
            smtp_port: this.smtpPort,
            smtp_username: this.smtpUsername,
            smtp_password: this.smtpPassword,
            smtp_domain: this.smtpDomain,
            smtp_from: this.smtpFrom,
            smtp_authentication: this.smtpAuthentication,
            smtp_enable_starttls: this.smtpEnableStarttls,
            smtp_ssl_verify: this.smtpSslVerify,
            aws_access_key_id: this.awsAccessKeyId,
            aws_secret_access_key: this.awsSecretAccessKey,
            aws_region: this.awsRegion,
            s3_attachments_bucket: this.s3AttachmentsBucket,
            gcs_credentials: this.gcsCredentials,
            gcs_project: this.gcsProject,
            gcs_bucket: this.gcsBucket,
            azure_storage_account_name: this.azureStorageAccountName,
            azure_storage_access_key: this.azureStorageAccessKey,
            azure_container: this.azureContainer,
          },
          extra: {
            title: this.$t("settings.instance_configuration", {
              instance: this.instanceName,
            }),
            description: this.$t("settings.configuring"),
            eventId,
          },
        }),
      );
      const err = res[0];

      if (err) {
        console.error(`error creating task ${taskAction}`, err);
        this.error.configureModule = this.getErrorMessage(err);
        this.loading.configureModule = false;
        return;
      }
    },
    configureModuleAborted(taskResult, taskContext) {
      console.error(`${taskContext.action} aborted`, taskResult);
      this.error.configureModule = this.$t("error.generic_error");
      this.loading.configureModule = false;
    },
    configureModuleCompleted() {
      this.loading.configureModule = false;

      // reload configuration
      this.getConfiguration();
    },
  },
};
</script>

<style scoped lang="scss">
@import "../styles/carbon-utils";
.mg-bottom {
  margin-bottom: $spacing-06;
}

.maxwidth {
  max-width: 38rem;
}
</style>
