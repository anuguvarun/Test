refreshDuplicateValidators() {
  this.cards.forEach((group, index) => {
    const search = group.get('search');
    if (search) {
      const existingValidator = search.validator;
      const duplicateValidator = duplicateSearchValidator(index, this.cards);
      const mergedValidator = Validators.compose([existingValidator, duplicateValidator]);
      search.setValidators(mergedValidator);
      search.updateValueAndValidity();
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