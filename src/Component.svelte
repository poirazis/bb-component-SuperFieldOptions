<script>
  import { getContext, onDestroy } from "svelte";
  import CellOptions from "../../bb_super_components_shared/src/lib/SuperTableCells/CellOptions.svelte";
  import "../../bb_super_components_shared/src/lib/SuperFieldsCommon.css";

  const { styleable, Provider, Block, BlockComponent } = getContext("sdk");
  const component = getContext("component");

  const formContext = getContext("form");
  const formStepContext = getContext("form-step");
  const labelPos = getContext("field-group");
  const labelWidth = getContext("field-group-label-width");
  const groupDisabled = getContext("field-group-disabled");
  const formApi = formContext?.formApi;

  export let field;
  export let controlType = "select";

  export let customButtons;
  export let buttons = [];
  export let buttonsQuiet = true;

  export let fieldLabel;
  export let span = 6;
  export let placeholder = "Choose Options";
  export let defaultValue;
  export let disabled;
  export let readonly;
  export let validation;
  export let onChange;
  export let debounced;
  export let debounceDelay;
  export let autocomplete;

  export let icon;

  export let optionsSource;
  export let datasource;
  export let limit;
  export let sortColumn;
  export let sortOrder;
  export let filter;
  export let prefetch;
  export let cache;
  export let valueColumn;
  export let labelColumn;
  export let columnList;
  export let colorColumn;
  export let iconColumn;
  export let customOptions;
  export let optionsArrangement;
  export let useOptionColors;
  export let optionsIcon;
  export let optionsViewMode;
  export let fullTable;

  let formField;
  let formStep;
  let fieldState;
  let fieldApi;
  let fieldSchema;
  let value;
  let cellState;

  $: formStep = formStepContext ? $formStepContext || 1 : 1;
  $: label = fieldLabel || fieldSchema?.name;

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

  $: value = fieldState?.value ? fieldState.value : defaultValue;

  $: $component.styles = {
    ...$component.styles,
    normal: {
      ...$component.styles.normal,
      "flex-direction": labelPos == "left" ? "row" : "column",
      gap: labelPos == "left" ? "0.85rem" : "0rem",
      "grid-column": labelPos ? "span " + span : null,
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
    optionsArrangement,
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
    fullTable,
    columnList,
    colorColumn,
    iconColumn,
    useOptionColors,
    optionsIcon,
    optionsViewMode,
    customOptions,
    role: "formInput",
    icon,
  };

  onDestroy(() => {
    fieldApi?.deregister();
    unsubscribe?.();
  });
</script>

<!-- svelte-ignore a11y-click-events-have-key-events -->
<!-- svelte-ignore a11y-no-noninteractive-tabindex -->
<Block>
  <div class="superField" use:styleable={$component.styles}>
    {#if label}
      <label for="superCell" class="superlabel" class:left={labelPos == "left"}>
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
        value={fieldState.value}
        multi
        on:change={(e) => {
          onChange?.({ value: e.detail });
          fieldApi?.setValue([...e.detail]);
        }}
        on:blur={cellState.lostFocus}
      />

      {#if customButtons && buttons?.length}
        <div
          class="spectrum-ActionGroup spectrum-ActionGroup--compact spectrum-ActionGroup--sizeM"
          class:spectrum-ActionGroup--quiet={buttonsQuiet}
        >
          <Provider data={{ value: fieldState.value }}>
            {#each buttons as { text, onClick }}
              <BlockComponent
                type="plugin/bb-component-SuperButton"
                props={{
                  size: "M",
                  disabled,
                  text,
                  onClick,
                }}
              ></BlockComponent>
            {/each}
          </Provider>
        </div>
      {/if}
    </div>
  </div>
</Block>
