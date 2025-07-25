onConfirmRemove(index: number): void {
  this.cards = this.cards.slice(0, index).concat(this.cards.slice(index + 1));
  delete this.errors[index];
  delete this.removals[index];
  this.removeCard.emit(index);

  if (this.cards.length === 0) {
    this.addCard();
  }

  this.calculateAdjustedBalance();

  // Safely refresh validators for all cards
  this.cards.forEach((card, idx) => {
    const search = card.get('search');
    if (search) {
      search.setValidators(duplicateSearchValidator(idx, this.cards));
      search.markAsTouched();
      search.updateValueAndValidity();
    }
  });
}