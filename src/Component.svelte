<script>
  import { getContext, onDestroy } from "svelte";
  import CellOptions from "../../bb_super_components_shared/src/lib/SuperTableCells/CellOptions.svelte";
  import SuperButton from "../../bb_super_components_shared/src/lib/SuperButton/SuperButton.svelte";
  import "../../bb_super_components_shared/src/lib/SuperFieldsCommon.css";

  const { styleable, enrichButtonActions } = getContext("sdk");
  const component = getContext("component");
  const allContext = getContext("context");

  const formContext = getContext("form");
  const formStepContext = getContext("form-step");
  const groupLabelPosition = getContext("field-group");
  const labelWidth = getContext("field-group-label-width");
  const groupColumns = getContext("field-group-columns");
  const groupDisabled = getContext("field-group-disabled");
  const formApi = formContext?.formApi;

  export let field;
  export let controlType = "select";
  export let role = "formInput";

  export let buttons = [];
  export let buttonsQuiet = true;

  export let label;
  export let span = 6;
  export let placeholder = "Choose Options";
  export let defaultValue;
  export let disabled;
  export let readonly;
  export let validation;
  export let onChange;
  export let debounced;
  export let debounceDelay = 750;
  export let autocomplete;

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
  export let prefetch;
  export let cache;
  export let valueColumn;
  export let labelColumn;
  export let colorColumn;
  export let iconColumn;
  export let customOptions;
  export let reorderOnly;
  export let useOptionColors;
  export let optionsIcon;
  export let optionsViewMode;
  export let direction;

  let formField;
  let formStep;
  let fieldState;
  let fieldApi;
  let fieldSchema;
  let value;
  let cellState;

  $: formStep = formStepContext ? $formStepContext || 1 : 1;
  $: label = label || fieldSchema?.name;
  $: labelPos =
    groupLabelPosition && labelPosition == "fieldGroup"
      ? groupLabelPosition
      : labelPosition;

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

  $: $component.styles = {
    ...$component.styles,
    normal: {
      ...$component.styles.normal,
      "flex-direction": labelPos == "left" ? "row" : "column",
      "align-items": "stretch",
      gap: labelPos == "left" ? "0.5rem" : "0rem",
      "grid-column": span < 7 ? "span " + span : "span " + groupColumns * 6,
      "--label-width":
        labelPos == "left" ? (labelWidth ? labelWidth : "6rem") : "auto",
    },
  };

  $: cellOptions = {
    disabled: disabled || groupDisabled || fieldState?.disabled,
    readonly: readonly || fieldState?.readonly,
    debounce: debounced ? debounceDelay : false,
    placeholder,
    defaultValue,
    padding: "0.5rem",
    autocomplete,
    error: fieldState.error,
    controlType,
    direction,
    optionsSource,
    datasource,
    limit,
    sortColumn,
    sortOrder,
    filter,
    prefetch,
    cache,
    valueColumn,
    labelColumn,
    colorColumn,
    iconColumn,
    useOptionColors,
    optionsIcon,
    optionsViewMode,
    customOptions,
    role,
    icon,
    showDirty,
    reorderOnly,
  };

  onDestroy(() => {
    fieldApi?.deregister();
    unsubscribe?.();
  });
</script>

<!-- svelte-ignore a11y-click-events-have-key-events -->
<!-- svelte-ignore a11y-no-noninteractive-tabindex -->
<!-- svelte-ignore a11y-no-noninteractive-element-interactions -->
<div
  class="superField"
  class:multirow={controlType != "select"}
  use:styleable={$component.styles}
>
  {#if label && labelPos}
    <label
      for="superCell"
      class="superlabel"
      class:left={labelPos == "left"}
      on:mousedown|preventDefault={cellState.focus}
    >
      {label}
      {#if fieldState.error}
        <div class="error" class:left={labelPos == "left"}>
          <span>{fieldState.error}</span>
        </div>
      {/if}
    </label>
  {/if}

  <div class="inline-cells">
    <CellOptions
      bind:cellState
      {cellOptions}
      {fieldSchema}
      {value}
      {autofocus}
      multi
      on:change={(e) => {
        onChange?.({ value: e.detail });
        fieldApi?.setValue([...e.detail]);
      }}
    />

    {#if buttons?.length}
      {#each buttons as { text, onClick, quiet, disabled, type }}
        <SuperButton
          quiet={buttonsQuiet || quiet}
          {disabled}
          size="M"
          {text}
          onClick={enrichButtonActions(onClick, $allContext)}
          emphasized
          selected={type == "cta"}
        />
      {/each}
    {/if}
  </div>
</div>
