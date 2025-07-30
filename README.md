Object.values(this.form.controls).forEach((group: AbstractControl) => {
  const searchControl = group.get('search');
  const tradeTypeControl = group.get('tradeType');

  searchControl?.valueChanges.subscribe(() => {
    this.triggerDuplicateValidation();
  });

  tradeTypeControl?.valueChanges.subscribe(() => {
    this.triggerDuplicateValidation();
  });
});

triggerDuplicateValidation() {
  Object.values(this.form.controls).forEach((group: AbstractControl) => {
    group.get('search')?.updateValueAndValidity({ onlySelf: true, emitEvent: false });
    group.get('tradeType')?.updateValueAndValidity({ onlySelf: true, emitEvent: false });
  });
}