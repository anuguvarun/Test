revalidation$ = new Subject<void>();

addCard(): void {
  this.cards.push(this.presenter.createForm());
  this.revalidation$.next(); // 🔁 triggers child to revalidate
}

onRemove(index: number): void {
  this.cards.splice(index, 1);
  this.revalidation$.next(); // 🔁 triggers child to revalidate
}