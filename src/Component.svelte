<script>
  import { getContext , onDestroy} from "svelte";
  import CellOptions from "../../bb_super_components_shared/src/lib/SuperTableCells/CellOptions.svelte";

  const { styleable, Provider, Block, BlockComponent } = getContext("sdk");
  const component = getContext("component");

  const formContext = getContext("form");
  const formStepContext = getContext("form-step");
  const labelPos = getContext("field-group");
  const labelWidth = getContext("field-group-label-width");
  const formApi = formContext?.formApi;

  export let field;
  export let controlType = "select"

  export let customButtons
  export let buttons = [];
  export let buttonsQuiet = true

  export let label;
  export let span = 6;
  export let placeholder = "Choose Options"
  export let defaultValue
  export let disabled
  export let readonly
  export let validation
  export let onChange
  export let autocomplete

  export let icon
  export let showIcon

  export let optionsSource
  export let datasource
  export let limit
  export let sortColumn
  export let sortOrder
  export let filter
  export let valueColumn
  export let labelColumn
  export let colorColumn
  export let iconColumn
  export let customOptions
  export let optionsArrangement
  export let useOptionColors
  export let useOptionIcons
  export let optionsViewMode

  let formField;
  let formStep;
  let fieldState;
  let fieldApi;
  let fieldSchema
  let value;
  let cellState

  $: formStep = formStepContext ? $formStepContext || 1 : 1;

  $: formField = formApi?.registerField(
    field,
    "array",
    defaultValue,
    disabled,
    readonly,
    validation,
    formStep
  )

  $: unsubscribe = formField?.subscribe((value) => {
    fieldState = value?.fieldState;
    fieldApi = value?.fieldApi;
    fieldSchema = value?.fieldSchema;
  });

  $: value = fieldState?.value ? fieldState.value : defaultValue

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
      disabled,
      readonly : readonly || disabled,
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
      valueColumn,
      labelColumn,
      colorColumn,
      iconColumn,
      useOptionColors,
      useOptionIcons,
      optionsViewMode,
      customOptions,
      role: "formInput", 
      icon,
    }
  
  onDestroy(() => {
    fieldApi?.deregister()
    unsubscribe?.()
  })
</script>

<!-- svelte-ignore a11y-click-events-have-key-events -->
<!-- svelte-ignore a11y-no-noninteractive-tabindex -->
<Block>
  <div
    class="superField"
    use:styleable={$component.styles}  
  >
    <label for="superCell" class="superlabel">
      {label}
        {#if fieldState.error}
          <div class="error">
            <span>{fieldState.error}</span>
          </div>
        {/if}
    </label>

    <div class="inline-cells">
      <CellOptions
        bind:cellState
        {cellOptions}
        {fieldSchema}
        value={fieldState.value} 
        multi
        on:change={(e) => { onChange?.( { value : e.detail } ); fieldApi?.setValue([...e.detail]); } }
        on:blur={cellState.lostFocus}
      />

      {#if customButtons && buttons?.length}
      <div
        class="spectrum-ActionGroup spectrum-ActionGroup--compact spectrum-ActionGroup--sizeM"
        class:spectrum-ActionGroup--quiet={buttonsQuiet}
      >
        <Provider data={ { value : fieldState.value }} >
          {#each buttons as { text, onClick }}
            <BlockComponent
              type = "plugin/bb-component-SuperButton"
              props = {{
                size: "M",
                disabled,
                text,
                onClick
              }}>
              </BlockComponent>
            {/each}
        </Provider>
      </div>
    {/if}
    </div>
  </div>

</Block>

<style>
  .superField {
    display: flex;
    align-items: stretch;
    justify-content: stretch;
    min-width: 0;
  }

  .superField:focus {
    outline: none;
  }
  .superlabel {
    display: flex;
  justify-content: space-between;
  align-items: center;
    overflow: hidden;
    text-overflow: ellipsis;
    white-space: nowrap;
    min-width: var(--label-width);
    max-width: var(--label-width);
    font-size: 13px;
    line-height: 1.75rem;
    font-weight: 400;
    color: var(--spectrum-global-color-gray-700);
  }

  .inline-cells {
    flex: 1;
    display: flex;
    justify-items: stretch;
  }

  .error {
    font-size: 12px;
    line-height: 1.75rem;
    color: var(--spectrum-global-color-red-700);
  }
</style>

