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