duplicateSearch(index?: number): void {
  console.log('🔍 Running duplicateSearch for index:', index);

  const targets: [FormGroup, number][] = typeof index === 'number'
    ? [[this.cards[index], index]]
    : this.cards.map((group, i) => [group, i]);

  targets.forEach(([group, actualIndex]) => {
    const search = group.get('search');
    if (search) {
      const existingValidator = search.validator;
      const duplicateValidator = duplicateSearchValidator(actualIndex, this.cards);

      const validators = [];
      if (existingValidator) validators.push(existingValidator);
      validators.push(duplicateValidator);

      const mergedValidator = Validators.compose(validators);
      search.setValidators(mergedValidator);
      search.updateValueAndValidity({ emitEvent: false });
    }
  });
}