@Input() set triggerValidationChange(value: any) {
  if (value !== undefined && this.search && this.cards) {
    // Create and run the duplicate validator directly
    const duplicateError = duplicateSearchValidator(this.index, this.cards)(this.search);

    const currentErrors = this.search.errors || {};

    if (duplicateError) {
      this.search.setErrors({ ...currentErrors, ...duplicateError });
    } else {
      // remove only 'duplicate' error, keep the rest
      const { duplicate, ...rest } = currentErrors;
      this.search.setErrors(Object.keys(rest).length ? rest : null);
    }
  }
}