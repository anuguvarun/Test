export function duplicateSearchValidator(index: number, cards: FormGroup[]): ValidatorFn {
  console.log('dup class cards', cards);

  return (control: AbstractControl): ValidationErrors | null => {
    const currentSearch = control.value?.trim()?.toLowerCase();
    const currentTradeType = cards[index].get('tradeType')?.value;

    // When only one card, we still call the validator, but return no error.
    if (cards.length <= 1 || !currentSearch || !currentTradeType) return null;

    const isDuplicate = cards.some((group, i) => {
      if (i === index) return false;

      const search = group.get('search')?.value?.trim()?.toLowerCase();
      const tradeType = group.get('tradeType')?.value;

      return search === currentSearch && tradeType === currentTradeType;
    });

    const xyz = isDuplicate ? { duplicate: true } : null;
    console.log('xyz', xyz);
    return xyz;
  };
}