# Dynamic Form Sample

* Built with kendo ui components
* Supports form groups 
* Built on top of angulars reactive form module

## Typescript Interfaces

* Defines the structure of the form that you want to render
* Can be either created locally or load from an external api (Depends on the use case)

```ts
export interface <MyPrefix>CustomFormGroup {
  groupName: string;
  groupLegend: string;
  formControls: <MyPrefix>CustomFormControls[];
}

export interface <MyPrefix>CustomFormControls {
  key: string;
  value: any;
  disabled: boolean;
  required: boolean;
  label: string;
  readonly: boolean;
  order: number;
}
```

## Creation of Angular FormGroup

* In order to create the angular formgroup for the `<form></form>` element the following code is used inside the components constructor

```ts
  public customFormGroupSample: FormGroup = new FormGroup({});
  public sampleFormGroups: <MyPrefix>CustomFormGroup[] = [
    {
      groupName: 'testGroup',
      groupLegend: 'Legend Title',
      formControls: [
        {
          key: 'test1',
          value: '',
          disabled: false,
          required: true,
          label: 'Test 1 Label',
          readonly: false,
          order: 1,
        },
        {
          key: 'test2',
          value: 'Test - 2',
          disabled: true,
          required: false,
          label: 'Disabled Sample Input',
          readonly: false,
          order: 2,
        },
      ],
    },
  ];

  constructor() {
    // Loop over each group that is defioned
    for (const group of this.sampleFormGroups) {
      // For each group create a new group that gets added to the main FormGroup
      const formGroup = new FormGroup({});
      // For each custom control add a new FormControl
      for (const control of group.formControls) {
        formGroup.addControl(
          control.key,
          control.required
            ? new FormControl(
                {
                  value: control.value,
                  disabled: control.disabled,
                },
                Validators.required
              )
            : new FormControl({
                value: control.value,
                disabled: control.disabled,
              })
        );
      }
      // Afterwards we add the whole group the the main FormGroup
      this.customFormGroupSample.addControl(group.groupName, formGroup);
    }
  }

  // Do something with the form afterwards when the user wants to submit
  public onSubmit() {
    console.log(this.customFormGroupSample.getRawValue());
  }
```

## Rendering of FormGroup with Dynamic Controls Component

* This is the main form that gets created.
* For each group it creates a new fieldset
* For each groups control it adds the controls based on the provided properties

```html
<div class="example">
  <form class="k-form" [formGroup]="customFormGroupSample" (ngSubmit)="onSubmit()">
    <fieldset class="k-form-fieldset" *ngFor="let group of sampleFormGroups">
      <legend class="k-form-legend">{{ group.groupLegend }}</legend>
      <div *ngFor="let control of group.formControls">
        <app-dynamic-form-sample [control]="control" [form]="customFormGroupSample" [formGroupName]="group.groupName"></app-dynamic-form-sample>
      </div>
    </fieldset>
  </form>
  <button type="submit" kendoButton [disabled]="!customFormGroupSample.valid" (click)="onSubmit()">Save</button>
</div>

```

**App Dynamic Form Sample Component**

```html
<div [formGroup]="form.controls[formGroupName]">
  <kendo-formfield>
    <kendo-label [for]="control.key"> {{ control.label }}</kendo-label>
    <kendo-textbox [formControlName]="control.key" [id]="control.key"></kendo-textbox>
    <kendo-formerror>Error: Something went wrong</kendo-formerror>
  </kendo-formfield>
</div>
```

```ts
@Input() public control: <MyPrefix>CustomFormControls;
@Input() public form!: FormGroup;
@Input() public formGroupName!: string;
```

