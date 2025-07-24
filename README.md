@Input() set triggerValidationChange(value: any) {
  if (value !== undefined) {
    this.search?.updateValueAndValidity({ onlySelf: true });
  }
}