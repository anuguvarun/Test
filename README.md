private subscribeToCard(card: FormGroup): void {
  const sub = card.valueChanges.subscribe(() => {
    this.triggerSiblingValidation(card);
  });
  this.cardSubscriptions.set(card, sub);
}



private rebuildSubscriptions(): void {
  this.cardSubscriptions.forEach(sub => sub.unsubscribe());
  this.cardSubscriptions.clear();

  this.cards.forEach(card => this.subscribeToCard(card));
}