private destroy$ = new Subject<void>();

ngOnInit(): void {
  this.card.get('actionToggle')?.valueChanges
    .pipe(takeUntil(this.destroy$))
    .subscribe(() => {
      this.updateTradeCount();
    });

  this.card.get('shareToggle')?.valueChanges
    .pipe(takeUntil(this.destroy$))
    .subscribe(() => {
      this.toggleClearValidate();
    });
}

ngOnDestroy(): void {
  this.destroy$.next();
  this.destroy$.complete();
}