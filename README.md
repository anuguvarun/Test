export function duplicateCusipAcrossCardsValidator(
  getCards: () => FormGroup[],
  getPositionsResponse: () => AccountPositionsResponseInfo | null
): ValidatorFn {
  return (control: AbstractControl): ValidationErrors | null => {
    const cards = getCards?.() ?? [];
    const group = control.parent as FormGroup;
    if (!cards.length || !group) return null;

    const resp = getPositionsResponse?.() ?? null;

    // Get the CUSIP for *this* card
    const currentCusip = resolveCusipFromResponse(group.get('search')?.value, resp);
    if (!currentCusip) return null;

    // Count how many cards resolve to the same CUSIP
    let sameCusipCount = 0;
    for (const card of cards) {
      const cusip = resolveCusipFromResponse(card.get('search')?.value, resp);
      if (cusip && cusip.toUpperCase() === currentCusip.toUpperCase()) {
        sameCusipCount++;
      }
    }

    // If more than one → duplicate
    return sameCusipCount > 1 ? { duplicateCusip: true } : null;
  };
}