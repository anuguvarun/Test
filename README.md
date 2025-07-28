setupDuplicateRevalidation() {
  this.cards.forEach((group, i) => {
    const searchControl = group.get('search');
    const tradeTypeControl = group.get('tradeType');

    if (searchControl && !searchControl['_revalidationAttached']) {
      searchControl.valueChanges.subscribe(() => {
        this.cards.forEach((g, j) => {
          if (i !== j) {
            g.get('search')?.updateValueAndValidity({ onlySelf: true });
          }
        });
      });
      searchControl['_revalidationAttached'] = true;
    }

    if (tradeTypeControl && !tradeTypeControl['_revalidationAttached']) {
      tradeTypeControl.valueChanges.subscribe(() => {
        this.cards.forEach((g, j) => {
          if (i !== j) {
            g.get('search')?.updateValueAndValidity({ onlySelf: true });
          }
        });
      });
      tradeTypeControl['_revalidationAttached'] = true;
    }
  });
}