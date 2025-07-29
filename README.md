this.cards.forEach((card, index, allCards) => {
  const search = card.get('search');

  if (search) {
    search.valueChanges.subscribe(() => {
      // Update all cards' search controls — not just the one that changed
      allCards.forEach((c) => {
        const s = c.get('search');
        if (s) {
          s.updateValueAndValidity({ onlySelf: true, emitEvent: false });
        }
      });
    });
  }
});