private cardSubscriptions = new Map<FormGroup, Subscription>();

addCard() {
  const newCard = this.fb.group({
    search: [''],
    tradeType: [''],
  });

  this.cards.push(newCard);

  // Set up valueChanges subscription
  const sub = newCard.valueChanges.subscribe(() => {
    this.triggerSiblingValidation(newCard);
  });

  this.cardSubscriptions.set(newCard, sub);
}

private triggerSiblingValidation(changedCard: FormGroup) {
  this.cards.forEach((card) => {
    if (card !== changedCard) {
      card.get('search')?.updateValueAndValidity({ onlySelf: true });
    }
  });
}

removeCard(index: number) {
  const removedCard = this.cards[index];

  // Unsubscribe to prevent memory leaks
  const sub = this.cardSubscriptions.get(removedCard);
  if (sub) {
    sub.unsubscribe();
    this.cardSubscriptions.delete(removedCard);
  }

  // Remove the card from the array
  this.cards.splice(index, 


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




