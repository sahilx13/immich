<script lang="ts">
  import { LogLevel, SystemConfigDto } from '@api';
  import { isEqual } from 'lodash-es';
  import { fade } from 'svelte/transition';
  import SettingButtonsRow from '../setting-buttons-row.svelte';
  import SettingSwitch from '../setting-switch.svelte';
  import SettingSelect from '../setting-select.svelte';
  import { createEventDispatcher } from 'svelte';
  import type { SettingsEventType } from '../admin-settings';

  export let savedConfig: SystemConfigDto;
  export let defaultConfig: SystemConfigDto;
  export let config: SystemConfigDto; // this is the config that is being edited
  export let disabled = false;

  const dispatch = createEventDispatcher<SettingsEventType>();
</script>

<div>
  <div in:fade={{ duration: 500 }}>
    <form autocomplete="off" on:submit|preventDefault>
      <div class="ml-4 mt-4 flex flex-col gap-4">
        <SettingSwitch title="ENABLED" {disabled} subtitle="Logging" bind:checked={config.logging.enabled} />
        <SettingSelect
          label="LEVEL"
          desc="When enabled, what log level to use."
          bind:value={config.logging.level}
          options={[
            { value: LogLevel.Fatal, text: 'Fatal' },
            { value: LogLevel.Error, text: 'Error' },
            { value: LogLevel.Warn, text: 'Warn' },
            { value: LogLevel.Log, text: 'Log' },
            { value: LogLevel.Debug, text: 'Debug' },
            { value: LogLevel.Verbose, text: 'Verbose' },
          ]}
          name="level"
          isEdited={config.logging.level !== savedConfig.logging.level}
          disabled={disabled || !config.logging.enabled}
        />

        <SettingButtonsRow
          on:reset={({ detail }) => dispatch('reset', { ...detail, configKeys: ['logging'] })}
          on:save={() => dispatch('save', { logging: config.logging })}
          showResetToDefault={!isEqual(savedConfig.logging, defaultConfig.logging)}
          {disabled}
        />
      </div>
    </form>
  </div>
</div>
