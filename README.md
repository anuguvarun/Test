private reapplyDuplicateValidators(): void {
  this.cards.forEach((formGroup, i) => {
    const control = formGroup.get('search');
    if (!control) return;

    const existingValidator = control.validator;
    const duplicateValidator = duplicateSearchValidator(i, this.cards);
    const mergedValidator = Validators.compose([existingValidator, duplicateValidator]);

    control.setValidators(mergedValidator);
    control.updateValueAndValidity({ onlySelf: true });
  });
}