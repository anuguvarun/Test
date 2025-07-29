this.cards.forEach((card) => {
  const search = card.get('search');

  if (search) {
    search.valueChanges.subscribe((newValue) => {
      // Highlight logic here
      console.log('Search value changed to:', newValue);
      // You can call `search.updateValueAndValidity()` if needed
      search.updateValueAndValidity();
    });
  }
});