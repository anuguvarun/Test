describe('calculateMaxAllowed', () => {
  const sut = new ExampleClass();
  const call = (v: number | null, vt: string, tt: string) =>
    (sut as any).calculateMaxAllowed(v, vt, tt) as number;

  it('returns 0 when value is null', () => {
    expect(call(null, 'anything', 'anything')).toBe(0);
  });

  it('handles 0 correctly (guards against NaN)', () => {
    expect(call(0, 'marketValue', SecurityType.FI)).toBe(0);
    expect(call(0, 'notMarketValue', 'Equity')).toBe(0);
  });

  it('for FI trades, returns value / 1000', () => {
    expect(call(1000, 'marketValue', SecurityType.FI)).toBe(1);
    expect(call(2500, 'notMarketValue', SecurityType.FI)).toBe(2.5);
  });

  it('for non-FI trades and valueType === "marketValue", applies the multiplier', () => {
    const MARKET_MULTIPLIER = 999_999; // <<< keep in sync with the one above
    expect(call(10, 'marketValue', 'Equity')).toBe(10 * MARKET_MULTIPLIER);
  });

  it('for non-FI trades and valueType !== "marketValue", returns the raw value', () => {
    expect(call(42, 'bookValue', 'Equity')).toBe(42);
  });

  it('works with negative values (document the intended behavior)', () => {
    // If negatives shouldn’t happen, change this test to assert clamping/throwing instead.
    expect(call(-3000, 'notMarketValue', 'Equity')).toBe(-3000);
    expect(call(-3000, 'marketValue', 'Equity')).toBeCloseTo(-3000 * 999_999); // update multiplier
  });
});