describe('duplicateSearchValidator (ticker symbols, extended coverage)', () => {
  const createGroup = (search: any, tradeType: any = 'BUY'): FormGroup =>
    new FormGroup({
      search: new FormControl(search),
      tradeType: new FormControl(tradeType)
    });

  it('should return null if search is undefined', () => {
    const control = new FormControl(undefined);
    const result = duplicateSearchValidator(() => [createGroup('AAPL')])(control);
    expect(result).toBeNull();
  });

  it('should return null if search is null', () => {
    const control = new FormControl(null);
    const result = duplicateSearchValidator(() => [createGroup('TSLA')])(control);
    expect(result).toBeNull();
  });

  it('should return null if search is empty string', () => {
    const control = new FormControl('');
    const result = duplicateSearchValidator(() => [createGroup('MSFT')])(control);
    expect(result).toBeNull();
  });

  it('should return null if search is only whitespace', () => {
    const control = new FormControl('   ');
    const result = duplicateSearchValidator(() => [createGroup('TSLA')])(control);
    expect(result).toBeNull();
  });

  it('should detect duplicate search + tradeType in a different index', () => {
    const cards = [
      createGroup('TSLA', 'BUY'),
      createGroup('GOOGL', 'BUY'),
      createGroup('TSLA', 'BUY') // duplicate of index 0
    ];
    const control = new FormControl('TSLA');
    const result = duplicateSearchValidator(() => cards)(control);
    // simulate being at index 2
    cards[2].parent = { controls: cards } as any;
    expect(result).toEqual({ duplicate: true });
  });

  it('should ignore same index match', () => {
    const cards = [
      createGroup('AAPL', 'BUY'),
      createGroup('GOOGL', 'BUY'),
      createGroup('MSFT', 'BUY')
    ];
    const control = new FormControl('GOOGL');
    const result = duplicateSearchValidator(() => cards)(control);
    // simulate being at index 1
    cards[1].parent = { controls: cards } as any;
    expect(result).toBeNull();
  });

  it('should treat lowercase and uppercase search as equal if tradeType matches', () => {
    const cards = [
      createGroup('amzn', 'SELL'),
      createGroup('AMZN', 'SELL')
    ];
    const control = new FormControl('AMZN');
    const result = duplicateSearchValidator(() => cards)(control);
    cards[1].parent = { controls: cards } as any;
    expect(result).toEqual({ duplicate: true });
  });

  it('should NOT return duplicate for same search with different tradeType', () => {
    const cards = [createGroup('NFLX', 'BUY'), createGroup('NFLX', 'SELL')];
    const control = new FormControl('NFLX');
    const result = duplicateSearchValidator(() => cards)(control);
    cards[1].parent = { controls: cards } as any;
    expect(result).toBeNull(); // No duplicate since tradeTypes differ
  });

  it('should trim and match duplicate search + tradeType', () => {
    const cards = [createGroup(' nflx ', 'BUY'), createGroup('NFLX', 'BUY')];
    const control = new FormControl('NFLX');
    const result = duplicateSearchValidator(() => cards)(control);
    cards[1].parent = { controls: cards } as any;
    expect(result).toEqual({ duplicate: true });
  });
});