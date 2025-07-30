export function duplicateSearchValidator(getCards: () => FormGroup[]): ValidatorFn {
  return (control: AbstractControl): ValidationErrors | null => {
    const cards = getCards();
    const group = control.parent as FormGroup;
    if (!group || !cards) return null;

    const currentSearch = group.get('search')?.value?.trim()?.toLowerCase();
    const currentTradeType = group.get('tradeType')?.value;
    if (!currentSearch || !currentTradeType) return null;

    // Count matching combinations
    let duplicates = 0;
    cards.forEach(card => {
      const search = card.get('search')?.value?.trim()?.toLowerCase();
      const tradeType = card.get('tradeType')?.value;

      if (search === currentSearch && tradeType === currentTradeType) {
        duplicates++;
      }
    });

    // Only set or remove 'duplicate' key, not whole error object
    const existingErrors = control.errors || {};

    if (duplicates > 1) {
      return {
        ...existingErrors,
        duplicate: true
      };
    } else {
      // Remove only the 'duplicate' key if it exists
      if ('duplicate' in existingErrors) {
        const { duplicate, ...rest } = existingErrors;
        return Object.keys(rest).length ? rest : null;
      }
      return existingErrors; // Might be null or still have other errors
    }
  };
}