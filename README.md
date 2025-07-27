duplicateSearch(index: number): void {
  const search = this.cards[index].get('search');
  if (!search) return;

  const existingValidatorFn = search.validator;

  const flattenedValidators = existingValidatorFn
    ? (Validators as any)._enabled(existingValidatorFn)
    : [];

  const composedValidator = Validators.compose([
    ...flattenedValidators,
    duplicateSearchValidator(index, this.cards),
  ]);

  search.setValidators(composedValidator);
  search.updateValueAndValidity();
}