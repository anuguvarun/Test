duplicateSearch(index: number): void {
  this.cards.forEach((card, i) => {
    const search = card.get('search');
    search.setValidators([
      Validators.required,
      duplicateSearchValidator(i, this.cards),
    ]);
    search.updateValueAndValidity({ onlySelf: true });
  });
}