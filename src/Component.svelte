<script>
  import { getContext, onDestroy } from "svelte";
  import CellOptions from "../../bb_super_components_shared/src/lib/SuperTableCells/CellOptions.svelte";
  import CellOptionsAdvanced from "../../bb_super_components_shared/src/lib/SuperTableCells/CellOptionsAdvanced.svelte";
  import SuperButton from "../../bb_super_components_shared/src/lib/SuperButton/SuperButton.svelte";
  import SuperFieldLabel from "../../bb_super_components_shared/src/lib/SuperFieldLabel/SuperFieldLabel.svelte";
  import "../../bb_super_components_shared/src/lib/SuperTableCells/CellCommon.css";
  import "../../bb_super_components_shared/src/lib/SuperFieldsCommon.css";

  const { styleable, enrichButtonActions, Provider } = getContext("sdk");
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
  export let customOptions;
  export let reorderOnly;
  export let optionsViewMode;
  export let direction;

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

  $: $component.styles = {
    ...$component.styles,
    normal: {
      ...$component.styles.normal,
      "grid-column": span < 7 ? "span " + span : "span " + groupColumns * 6,
    },
  };

  $: cellOptions = {
    disabled: disabled || groupDisabled || fieldState?.disabled,
    readonly: readonly || fieldState?.readonly,
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
<div use:styleable={$component.styles}>
  <Provider data={{ value }} />
  <div
    class="superField"
    class:left-label={labelPos == "left"}
    class:multirow={controlType != "select"}
  >
    <SuperFieldLabel
      {labelPos}
      {labelWidth}
      {label}
      {helpText}
      error={fieldState?.error}
    />

    <div class="inline-cells" class:multirow>
      {#if controlType == "select" || controlType == "inputSelect"}
        <CellOptions
          {cellOptions}
          {fieldSchema}
          {value}
          {autofocus}
          multi
          on:change={(e) => {
            onChange?.({ value: e.detail });
            value = e.detail;
            fieldApi?.setValue([...e.detail]);
          }}
        />
      {:else}
        <CellOptionsAdvanced
          {cellOptions}
          {fieldSchema}
          {value}
          {autofocus}
          multi
          on:change={(e) => {
            onChange?.({ value: e.detail });
            value = e.detail;
            fieldApi?.setValue([...e.detail]);
          }}
        />
      {/if}

      {#if buttons?.length && controlType != "list"}
        <div class="inline-buttons" class:vertical={multirow}>
          {#each buttons as { text, onClick, quiet, disabled, type, size }}
            <SuperButton
              {quiet}
              {disabled}
              {size}
              {type}
              {text}
              on:click={enrichButtonActions(onClick, $allContext)({ value })}
            />
          {/each}
        </div>
      {/if}
    </div>
  </div>
</div>
