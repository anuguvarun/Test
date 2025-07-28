private duplicateValidators: { [index: number]: ValidatorFn } = {};

duplicateSearch(index: number): void {
  const search = this.cards[index].get('search');
  if (!search) return;

  // Remove existing duplicate validator if present
  let currentValidators = [];

  if (search.validator) {
    const validatorFn = search.validator as ValidatorFn;

    // Decompose composed validators if needed
    const tempControl = new FormControl();
    tempControl.setValidators(validatorFn);
    currentValidators = (tempControl.validator ? [tempControl.validator] : []);
  }

  // Create new duplicate validator and store
  const newDuplicateValidator = duplicateSearchValidator(index, this.cards);
  this.duplicateValidators[index] = newDuplicateValidator;

  // Compose new list (you can filter existing ones if needed)
  const validators = [
    Validators.required,
    newDuplicateValidator
  ];

  search.setValidators(validators);
  search.updateValueAndValidity();
}






removeDuplicateValidator(index: number): void {
  const search = this.cards[index].get('search');
  if (!search) return;

  search.setValidators(Validators.required); // or any other default set
  search.updateValueAndValidity();
  delete this.duplicateValidators[index];
}
