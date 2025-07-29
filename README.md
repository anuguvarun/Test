import { debounceTime, takeUntil, distinctUntilChanged } from 'rxjs/operators';

this.cards.forEach((card, index, allCards) => {
  const search = card.get('search');

  if (search) {
    search.valueChanges
      .pipe(
        debounceTime(150),
        distinctUntilChanged(),
        takeUntil(this.destroy$)
      )
      .subscribe((value: string) => {
        const normalized = value?.trim().toLowerCase();
        const tradeType = card.get('tradeType')?.value;

        if (!normalized || !tradeType) return;

        // Check if duplicate exists
        const duplicates = allCards.filter((c) => {
          const s = c.get('search')?.value?.trim().toLowerCase();
          const t = c.get('tradeType')?.value;
          return s === normalized && t === tradeType;
        });

        if (duplicates.length > 1) {
          console.log('Duplicate detected for:', normalized);

          // Trigger update only for those involved
          duplicates.forEach((c) => {
            const s = c.get('search');
            if (s) {
              s.updateValueAndValidity({ onlySelf: true, emitEvent: false });
            }
          });
        }
      });
  }
});