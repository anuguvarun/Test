@Input() set triggerDuplicateCheck(val: any) {
  if (val !== undefined && this.search && this.cards) {
    // Call duplicate validator inline
    const validator = duplicateSearchValidator(this.index, this.cards);
    const error = validator(this.search);

    // Merge or remove 'duplicate' error only
    const currentErrors = this.search.errors || {};

    if (error?.duplicate) {
      this.search.setErrors({ ...currentErrors, duplicate: true });
    } else {
      const { duplicate, ...rest } = currentErrors;
      this.search.setErrors(Object.keys(rest).length ? rest : null);
    }
  }
}