it('should return null if tradeType is missing', () => {
  const group = new FormGroup({
    search: new FormControl('TSLA')
    // no tradeType field
  });
  const control = group.get('search')!;
  const result = duplicateSearchValidator(() => [group])(control);
  expect(result).toBeNull();
});