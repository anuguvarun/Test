revalidateAllCards(): void {
  this.cards.forEach((formGroup, index) => {
    const control = formGroup.get('search');
    if (!control) return;

    // Preserve existing validators
    const baseValidators = [
      Validators.required,
      searchValidator(() => formGroup.get('tradeType')?.value),
      securityResponseValidator(
        () => formGroup.get('securityPriceData')?.value,
        () => formGroup.get('searchSent')?.value
      ),
      duplicateSearchValidator(index, this.cards),
    ];

    control.setValidators(baseValidators);
    control.updateValueAndValidity({ onlySelf: true });
  });
}