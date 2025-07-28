revalidation$ = new Subject<void>();

addCard(): void {
  this.cards.push(this.presenter.createForm());
  this.revalidation$.next(); // 🔁 triggers child to revalidate
}

onRemove(index: number): void {
  this.cards.splice(index, 1);
  this.revalidation$.next(); // 🔁 triggers child to 


refreshCardValidators(): void {
  this.cards.forEach((card, index) => {
    const search = card.get('search');
    if (!search) return;

    const validator = duplicateSearchValidator(index, this.cards);
    search.setValidators([Validators.required, validator]);
    search.updateValueAndValidity({ onlySelf: true });
  });
}



