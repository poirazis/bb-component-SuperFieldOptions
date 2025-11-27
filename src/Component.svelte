<script>
  import { getContext, onDestroy } from "svelte";
  import {
    CellOptions,
    CellOptionsAdvanced,
    SuperButton,
    SuperField,
  } from "@poirazis/supercomponents-shared";

  const { styleable, enrichButtonActions, Provider, builderStore } =
    getContext("sdk");
  const component = getContext("component");
  const allContext = getContext("context");

  const formContext = getContext("form");
  const formStepContext = getContext("form-step");
  const groupLabelPosition = getContext("field-group");
  const labelWidth = getContext("field-group-label-width");
  const groupColumns = getContext("field-group-columns");
  const groupDisabled = getContext("field-group-disabled");
  const formApi = formContext?.formApi;

  export let field = "Options Field";
  export let controlType = "select";
  export let role = "formInput";

  export let buttons = [];

  export let label = "Options Field";
  export let span = 6;
  export let placeholder = "Choose Options";
  export let defaultValue;
  export let disabled;
  export let readonly;
  export let autocomplete = true;
  export let validation;
  export let invisible = false;
  export let onChange;
  export let debounced;
  export let debounceDelay = 250;
  export let helpText;

  export let icon;
  export let labelPosition = "fieldGroup";
  export let showDirty;
  export let autofocus;

  export let optionsSource = "schema";
  export let datasource;
  export let limit;
  export let sortColumn;
  export let sortOrder;
  export let filter;
  export let valueColumn;
  export let labelColumn;
  export let iconColumn;
  export let colorColumn;
  export let customOptions;
  export let reorderOnly;
  export let optionsViewMode;
  export let direction;
  export let toggleAll;

  let formField;
  let formStep;
  let fieldState;
  let fieldApi;
  let fieldSchema;
  let value;

  $: multirow = controlType != "select" && controlType != "inputSelect";
  $: formStep = formStepContext ? $formStepContext || 1 : 1;
  $: labelPos = field
    ? groupLabelPosition && labelPosition == "fieldGroup"
      ? groupLabelPosition
      : labelPosition
    : false;

  $: formField = formApi?.registerField(
    field,
    "array",
    defaultValue,
    disabled,
    readonly,
    validation,
    formStep
  );

  $: unsubscribe = formField?.subscribe((value) => {
    fieldState = value?.fieldState;
    fieldApi = value?.fieldApi;
    fieldSchema = value?.fieldSchema;
  });

  $: value = fieldState?.value;
  $: error = fieldState?.error;

  $: $component.styles = {
    ...$component.styles,
    normal: {
      ...$component.styles.normal,
      display:
        invisible && !$builderStore.inBuilder
          ? "none"
          : $component.styles.normal.display,
      opacity: invisible && $builderStore.inBuilder ? 0.6 : 1,
      "grid-column": groupColumns ? `span ${span}` : "span 1",
      overflow: "hidden",
    },
  };

  $: cellOptions = {
    disabled: disabled || groupDisabled || fieldState?.disabled,
    readonly: readonly || fieldState?.readonly,
    autocomplete,
    debounce: debounced ? debounceDelay : false,
    placeholder,
    defaultValue,
    error: fieldState?.error,
    controlType,
    direction,
    optionsSource,
    datasource,
    limit,
    sortColumn,
    sortOrder,
    filter,
    valueColumn,
    labelColumn,
    iconColumn,
    colorColumn,
    optionsViewMode,
    customOptions,
    role,
    icon,
    showDirty,
    reorderOnly,
    toggleAll,
  };

  const handleChange = async (newValue) => {
    value = newValue;
    fieldApi?.setValue(newValue);
    await onChange?.({ value: newValue });
  };

  onDestroy(() => {
    fieldApi?.deregister();
    unsubscribe?.();
  });
</script>

<!-- svelte-ignore a11y-click-events-have-key-events -->
<!-- svelte-ignore a11y-no-noninteractive-tabindex -->
<!-- svelte-ignore a11y-no-noninteractive-element-interactions -->
<div use:styleable={$component.styles}>
  <Provider data={{ value }} />
  <SuperField
    {multirow}
    {labelPos}
    {labelWidth}
    {field}
    {label}
    {error}
    {helpText}
  >
    {#key customOptions}
      {#if controlType == "select" || controlType == "inputSelect"}
        <CellOptions
          {cellOptions}
          {fieldSchema}
          {value}
          {autofocus}
          multi={true}
          on:change={(e) => handleChange(e.detail)}
        />
      {:else}
        <CellOptionsAdvanced
          {cellOptions}
          {fieldSchema}
          {value}
          {autofocus}
          label={labelPos ? null : label}
          multi={true}
          on:change={(e) => handleChange(e.detail)}
        />
      {/if}
    {/key}
    {#if buttons?.length && controlType == "select"}
      <div class="inline-buttons">
        {#each buttons as { text, onClick, quiet, disabled, type, size }}
          <SuperButton
            {quiet}
            {disabled}
            {size}
            {type}
            {text}
            onClick={enrichButtonActions(onClick, $allContext)}
          />
        {/each}
      </div>
    {/if}
  </SuperField>
</div>
