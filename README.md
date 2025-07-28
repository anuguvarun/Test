function duplicateSearchValidator(getCards: () => FormGroup[]): ValidatorFn {
  return (control: AbstractControl): ValidationErrors | null => {
    const cards = getCards();
    const group = control.parent as FormGroup;
    const index = cards.indexOf(group);
    if (!cards || index === -1) return null;

    const currentSearch = control.value?.trim()?.toLowerCase();
    const currentTradeType = group.get('tradeType')?.value;
    if (!currentSearch || !currentTradeType) return null;

    // Find all indexes with same search + tradeType
    const duplicates = cards
      .map((g, i) => ({
        index: i,
        search: g.get('search')?.value?.trim()?.toLowerCase(),
        tradeType: g.get('tradeType')?.value
      }))
      .filter(
        item =>
          item.search === currentSearch &&
          item.tradeType === currentTradeType
      )
      .map(item => item.index);

    // If current index is NOT the first in the list of duplicates => show error
    if (duplicates.length > 1 && index !== duplicates[0]) {
      return { duplicate: true };
    }

    return null;
  };
}