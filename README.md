this.cards.forEach((card, index) => {
  const control = card.get('search');
  control?.valueChanges.subscribe(() => {
    this.duplicateSymbol(index);
  });
});