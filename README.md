function duplicateSearchValidator(getCards: () => FormGroup[]): ValidatorFn {
  return (control: AbstractControl): ValidationErrors | null => {
    const cards = getCards();
    const group = control.parent as FormGroup;
    const index = cards.indexOf(group); // 👈 dynamic index

    if (!cards || index === -1) return null;

    const currentSearch = control.value?.trim()?.toLowerCase();
    const currentTradeType = cards[index].get('tradeType')?.value;

    if (!currentSearch || !currentTradeType) return null;

    const isDuplicate = cards.some((g, i) => {
      if (i === index) return false;
      const search = g.get('search')?.value?.trim()?.toLowerCase();
      const tradeType = g.get('tradeType')?.value;
      return search === currentSearch && tradeType === currentTradeType;
    });

    return isDuplicate ? { duplicate: true } : null;
  };
}