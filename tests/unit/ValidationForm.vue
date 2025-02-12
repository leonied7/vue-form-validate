<template>
  <validation-provider
    :default-values="defaultValues"
    :default-errors="defaultErrors"
    :resolver="resolver"
    :reset-on-update="resetOnUpdate"
    @submit="onSubmit"
    @dirty="$emit('dirty', $event)"
    @change="$emit('change', $event)"
  >
    <template
      #default="{
        handleSubmit,
        reset,
        onFieldChange,
        values,
        dirty: formDirty,
        pristine: formPristine,
        invalid: formInvalid,
        errors: formErrors,
        setError
      }"
    >
      <form @submit.prevent="handleSubmit">
        <ValidationField
          v-if="$options.get(values, 'my.nested.value') === 'test'"
          key="1"
          name="my-input"
        >
          <template
            #default="{ modelValue, name, firstError, errors, dirty, pristine, invalid, onChange }"
          >
            <base-input
              ref="myInputValueFirst"
              :name="name"
              :first-error="firstError"
              :errors="errors"
              :model-value="modelValue"
              :dirty="dirty"
              :pristine="pristine"
              :invalid="invalid"
              @update:model-value="onChange"
            />
          </template>
        </ValidationField>
        <ValidationField
          v-else
          key="2"
          name="my-input"
        >
          <template
            #default="{ modelValue, name, firstError, errors, dirty, pristine, invalid, onChange }"
          >
            <base-input
              ref="myInputValueSecond"
              :name="name"
              :first-error="firstError"
              :errors="errors"
              :model-value="modelValue"
              :dirty="dirty"
              :pristine="pristine"
              :invalid="invalid"
              @update:model-value="onChange"
            />
          </template>
        </ValidationField>

        <ValidationField name="my.nested.value">
          <template
            #default="{ modelValue, name, firstError, errors, dirty, pristine, invalid, onChange }"
          >
            <base-input
              ref="myNestedValueInput"
              :name="name"
              :first-error="firstError"
              :errors="errors"
              :model-value="modelValue"
              :dirty="dirty"
              :pristine="pristine"
              :invalid="invalid"
              @update:model-value="onChange"
            />
          </template>
        </ValidationField>

        <ValidationFieldArray name="arrayField">
          <template
            #default="{
              name: arrayName,
              onChange: onArrayChange,
              fields,
              append,
              prepend,
              insert,
              swap,
              move,
              remove
            }"
          >
            <div
              v-for="(field, index) in fields"
              :key="field.id"
            >
              <ValidationField :name="`${arrayName}.${index}.id`" />
              <ValidationField :name="`${arrayName}.${index}.type`" />
              <ValidationField
                :name="`${arrayName}.${index}.firstName`"
                @should-focus="$emit('focus', $event)"
              >
                <template
                  #default="{
                    modelValue,
                    name,
                    firstError,
                    errors,
                    dirty,
                    pristine,
                    invalid,
                    onChange
                  }"
                >
                  <base-input
                    :name="name"
                    :first-error="firstError"
                    :errors="errors"
                    :model-value="modelValue"
                    :dirty="dirty"
                    :pristine="pristine"
                    :invalid="invalid"
                    @update:model-value="onChange"
                  />
                </template>
              </ValidationField>
            </div>
            <button
              id="append"
              type="button"
              @click="append({ firstName: 'new name' }, { field: 'firstName' })"
            >
              Append
            </button>
            <button
              id="prepend"
              type="button"
              @click="prepend({ firstName: 'new name' }, { field: 'firstName' })"
            >
              Prepend
            </button>
            <button
              id="insert"
              type="button"
              @click="insert(1, { firstName: 'insert' }, { field: 'firstName' })"
            >
              Insert
            </button>
            <button
              id="swap"
              type="button"
              @click="swap(0, 2, { field: 'firstName' })"
            >
              Swap
            </button>
            <button
              id="move"
              type="button"
              @click="move(0, 2, { field: 'firstName' })"
            >
              Move
            </button>
            <button
              id="remove"
              type="button"
              @click="remove(1, { field: 'firstName' })"
            >
              Remove
            </button>
            <button
              id="arrayChange"
              type="button"
              @click="onArrayChange([{ id: 42 }, { id: 1, firstName: '' }, { id: 2, firstName: '' }])"
            >
              Change array
            </button>
          </template>
        </ValidationFieldArray>

        <ValidationErrors name="common">
          <template #default="{ errors, submitted }">
            <BaseErrors
              :errors="errors"
              :submitted="submitted"
            />
          </template>
        </ValidationErrors>

        <button
          id="setError"
          type="button"
          @click="setError('common', { message: 'test' })"
        >
          Set error
        </button>

        <FormInfo
          :values="values"
          :dirty="formDirty"
          :pristine="formPristine"
          :invalid="formInvalid"
          :errors="formErrors"
          @set-field-value="onFieldChange"
        />

        <button type="submit">
          Submit
        </button>
        <button
          type="reset"
          @click="reset($event.payload)"
        >
          Reset
        </button>
      </form>
    </template>
  </validation-provider>
</template>

<script lang="ts">
import {
  ValidationProvider,
  ValidationField,
  ValidationFieldArray,
  ValidationErrors,
  get
} from '../../src';
import BaseInput from './BaseInput.vue';
import FormInfo from './FormInfo.vue';
import BaseErrors from './BaseErrors.vue';

export default {
  name: 'ValidationForm',
  get,
  components: {
    BaseErrors,
    FormInfo,
    ValidationProvider,
    ValidationField,
    ValidationFieldArray,
    ValidationErrors,
    BaseInput
  },
  inheritAttrs: false,
  props: {
    defaultValues: {
      type: Object,
      default: () => ({})
    },
    defaultErrors: {
      type: Object,
      default: () => ({})
    },
    resolver: {
      type: Function,
      default: undefined
    },
    resetOnUpdate: {
      type: Boolean,
      default: true
    }
  },
  emits: {
    submit: null,
    dirty: null,
    change: null,
    focus: null
  },
  methods: {
    onSubmit(values, options) {
      this.$emit('submit', values, options);
    }
  }
};
</script>
