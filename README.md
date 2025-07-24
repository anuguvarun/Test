@Input() set triggerValidationChange(value: any) {
  if (value !== undefined && this.duplicateValidatorFn && this.search) {
    const error = this.duplicateValidatorFn(this.search);

    const currentErrors = this.search.errors || {};

    if (error) {
      this.search.setErrors({ ...currentErrors, ...error });
    } else {
      // remove only the 'duplicate' error, leave others untouched
      const { duplicate, ...rest } = currentErrors;
      this.search.setErrors(Object.keys(rest).length ? rest : null);
    }
  }
}