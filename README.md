refreshDuplicateValidators() {
  console.log('Refreshing...');
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

      // Only validate silently
      search.updateValueAndValidity({ emitEvent: false }); // 🔥 Prevents recursion
    }
  });
}


addCard(): void {
  this.tradeAdded.emit();

  const form = this.presenter.createForm();
  this.cards.push(form);

  const searchControl = form.get('search');
  if (searchControl) {
    searchControl.valueChanges.subscribe(() => {
      this.refreshDuplicateValidators();
    });
  }

  this.removals = [];
}


export function duplicateSearchValidator(index: number, cards: FormGroup[]): ValidatorFn {
  return (control: AbstractControl): ValidationErrors | null => {
    const currentValue = control.value?.trim()?.toLowerCase();
    if (!currentValue) return null;

    // Check if any previous card already has the same value
    const isDuplicate = cards.slice(0, index).some(group => {
      const value = group.get('search')?.value?.trim()?.toLowerCase();
      return value === currentValue;
    });

    return isDuplicate ? { duplicate: true } : null;
  };
}