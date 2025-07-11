import { AbstractControl, ValidatorFn } from '@angular/forms';

function duplicateCardValueValidator(index: number, cards: FormGroup[]): ValidatorFn {
  return (control: AbstractControl): { [key: string]: any } | null => {
    const currentValue = control.value?.trim()?.toLowerCase();
    if (!currentValue) return null;

    const isDuplicate = cards.some((group, i) => {
      if (i === index) return false;
      const value = group.get('cardValue')?.value?.trim()?.toLowerCase();
      return value === currentValue;
    });

    return isDuplicate ? { duplicate: true } : null;
  };
}
this.cards.forEach((group, index) => {
  const control = group.get('cardValue');
  control?.setValidators([
    Validators.required,
    duplicateCardValueValidator(index, this.cards)
  ]);

  control?.valueChanges.subscribe(() => {
    // Re-validate all controls to reflect latest state
    this.cards.forEach((g, i) => {
      const c = g.get('cardValue');
      c?.updateValueAndValidity({ onlySelf: true, emitEvent: false });
    });
  });
});
