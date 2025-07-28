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
  this.cards.splice(index, 1);
}