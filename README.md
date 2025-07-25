reapplyDuplicateValidators(): void {
  this.cards.forEach((formGroup, index) => {
    const search = formGroup.get('search');
    if (!search) return;

    const existingValidator = search.validator;
    const duplicateValidator = duplicateSearchValidator(index, this.cards);
    const merged = Validators.compose([existingValidator, duplicateValidator]);

    search.setValidators(merged);
    search.updateValueAndValidity({ onlySelf: true });
  });
}