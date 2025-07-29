this.cards.forEach((card, index, allCards) => {
  const search = card.get('search');

  if (search) {
    // Attach the validator once
    search.setValidators([duplicateSearchValidator(() => this.cards)]);

    // Subscribe to changes
    search.valueChanges
      .pipe(takeUntil(this.destroy$))
      .subscribe(() => {
        // Trigger validator re-run
        search.updateValueAndValidity({ onlySelf: true, emitEvent: false });

        // ✅ Use the validator result — no duplication
        const hasDuplicateError = search.errors?.['duplicate'];

        if (hasDuplicateError) {
          console.log('clear (duplicate)');
          search.updateValueAndValidity({ onlySelf: true, emitEvent: false });

          // Optional: you can do more here, like notify user, disable button, etc.
        }
      });
  }
});