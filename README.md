private cardSubscriptions = new Map<FormGroup, Subscription>();

private triggerSiblingValidation(changedCard: FormGroup) {
  this.cards.forEach((card) => {
    if (card !== changedCard) {
      card.get('search')?.updateValueAndValidity({ onlySelf: true });
    }
  });
}

addCard(): void {
  this.tradeAdded.emit();

  const newForm = this.presenter.createForm();
  this.cards.push(newForm);
  this.removals = [];

  const sub = newForm.valueChanges.subscribe(() => {
    this.triggerSiblingValidation(newForm);
  });

  this.cardSubscriptions.set(newForm, sub);
}

onConfirmRemove(index: number): void {
  const removedCard = this.cards[index];

  // 1. Unsubscribe from the removed card's valueChanges
  const sub = this.cardSubscriptions.get(removedCard);
  if (sub) {
    sub.unsubscribe();
    this.cardSubscriptions.delete(removedCard);
  }

  // 2. Update the cards array
  this.cards = this.cards.slice(0, index).concat(this.cards.slice(index + 1));
  delete this.errors[index];
  delete this.removals[index];
  this.removeCard.emit(index);

  // 3. If empty, add a new card
  if (this.cards.length === 0) {
    this.addCard();
  }

  // 4. Recalculate balance and rewire subscriptions
  this.calculateAdjustedBalance();
  this.rebuildSubscriptions(); // 👈 Add this helper function
}

private rebuildSubscriptions(): void {
  // Clear any old subs just in case
  this.cardSubscriptions.forEach(sub => sub.unsubscribe());
  this.cardSubscriptions.clear();

  this.cards.forEach(card => {
    const sub = card.valueChanges.subscribe(() => {
      this.triggerSiblingValidation(card);
    });
    this.cardSubscriptions.set(card, sub);
  });
}
