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