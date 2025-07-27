import {
  AbstractControl,
  FormGroup,
  ValidationErrors,
  ValidatorFn,
} from '@angular/forms';

export function duplicateSearchValidator(
  index: number,
  cards: FormGroup[]
): ValidatorFn {
  return (control: AbstractControl): ValidationErrors | null => {
    // Don't validate if not enough items to compare
    if (cards.length <= 1) return null;

    const currentSearch = control.value?.trim()?.toLowerCase();
    const currentTradeType = cards[index].get('tradeType')?.value;

    if (!currentSearch || !currentTradeType) return null;

    const isDuplicate = cards.some((group, i) => {
      if (i === index) return false;

      const search = group.get('search')?.value?.trim()?.toLowerCase();
      const tradeType = group.get('tradeType')?.value;

      return search === currentSearch && tradeType === currentTradeType;
    });

    // ✅ Always return a new object or null — no caching!
    return isDuplicate ? { duplicate: true } : null;
  };
}