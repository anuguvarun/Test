export function duplicateCardValueValidator(
  index: number,
  cards: FormGroup[],
  fieldName: string
): ValidatorFn {
  return (control: AbstractControl): { [key: string]: any } | null => {
    const currentValue = control.value?.trim()?.toLowerCase();
    if (!currentValue) return null;

    const isDuplicate = cards.some((group, i) => {
      if (i === index) return false;
      const value = group.get(fieldName)?.value?.trim()?.toLowerCase();
      return value === currentValue;
    });

    return isDuplicate ? { duplicate: true } : null;
  };
}

duplicateSymbol(index: number): void {
  const applyValidators = (controlName: string) => {
    const control = this.cards[index].get(controlName);
    if (control) {
      control.setValidators([
        Validators.required,
        duplicateCardValueValidator(index, this.cards, controlName),
      ]);
      control.updateValueAndValidity();
    }
  };

  applyValidators('search');
  applyValidators('cusip');
}
