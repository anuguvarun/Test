private destroy$ = new Subject<void>();

this.cards.forEach((card, index, allCards) => {
  const search = card.get('search');
  
  if (search) {
    search.valueChanges
      .pipe(takeUntil(this.destroy$))
      .subscribe(() => {
        allCards.forEach((c) => {
          const s = c.get('search');
          if (s) {
            console.log('clear');
            s.updateValueAndValidity({ onlySelf: true, emitEvent: false });
          }
        });
      });
  }
});


ngOnDestroy(): void {
  this.destroy$.next();
  this.destroy$.complete();
}