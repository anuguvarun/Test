duplicateSearch(): void {
  this.cards.forEach((card) => {
    const search = card.get('search');
    search.setValidators([
      Validators.required,
      duplicateSearchValidator(-1, this.cards), 
    ]);
    search.updateValueAndValidity({ onlySelf: true });
  });
}