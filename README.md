export function duplicateSearchValidator(index: number, cards: FormGroup[]): ValidatorFn {
  return (control: AbstractControl): ValidationErrors | null => {
    const currentValue = control.value?.trim()?.toLowerCase();
    if (!currentValue) return null;

    const isDuplicate = cards
      .slice(0, index) // Only check cards before this one
      .some(group => {
        const value = group.get('search')?.value?.trim()?.toLowerCase();
        return value === currentValue;
      });

    return isDuplicate ? { duplicate: true } : null;
  };
}

refreshDuplicateValidators(): void {
  console.log('🔁 Refreshing duplicate validators...');

  this.cards.forEach((group, index) => {
    const search = group.get('search');
    if (search) {
      const duplicateValidator = duplicateSearchValidator(index, this.cards);

      const existingValidator = search.validator;
      const validators = [];

      if (existingValidator) validators.push(existingValidator);
      validators.push(duplicateValidator);

      const mergedValidator = Validators.compose(validators);
      search.setValidators(mergedValidator);
      search.updateValueAndValidity({ emitEvent: false }); // Prevent recursion
    }
  });
}

addCard(): void {
  const form = this.presenter.createForm();
  this.cards.push(form);

  const search = form.get('search');
  if (search) {
    search.valueChanges
      .pipe(debounceTime(0)) // Wait for Angular to update form first
      .subscribe(() => this.refreshDuplicateValidators());
  }

  this.refreshDuplicateValidators(); // in case it causes new duplicates
}



onConfirmRemove(index: number): void {
  this.cards.splice(index, 1);
  this.refreshDuplicateValidators(); // 🔥 Always refresh after removal
}

