component.accountPositions = {
  accountPositions: [
    {
      positions: [
        {
          symbol: 'MSFT',
          quantity: 50,
          description: 'Microsoft Corp',
          type: 'Equity',
          lastPrice: 300,
          marketValue: 15000,
          holdingPct: 0.25
        }
      ]
    }
  ]
};

const createMockPosition = (symbol: string, quantity: number | null) => ({
  symbol,
  quantity,
  description: 'Test Description',
  type: 'Equity',
  lastPrice: 100,
  marketValue: 10000,
  holdingPct: 0.1
});

positions: [createMockPosition('AAPL', 204)]