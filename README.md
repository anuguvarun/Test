export function duplicateSearchValidator(getCards: () => FormGroup[]): ValidatorFn {
  return (control: AbstractControl): ValidationErrors | null => {
    const cards = getCards();
    const group = control.parent as FormGroup;
    const index = cards.indexOf(group);

    if (!cards || index === -1) return null;

    const currentSearch = control.value?.trim()?.toLowerCase();
    const currentTradeType = group.get('tradeType')?.value;

    if (!currentSearch || !currentTradeType) return null;

    // Count matching combinations
    let duplicatesCount = 0;

    cards.forEach((card, i) => {
      const search = card.get('search')?.value?.trim()?.toLowerCase();
      const tradeType = card.get('tradeType')?.value;

      if (search === currentSearch && tradeType === currentTradeType) {
        duplicatesCount++;
      }
    });

    // Show error if there’s more than 1 match (i.e., duplicate found)
    if (duplicatesCount > 1) {
      return { duplicate: true };
    }

    return null;
  };
}