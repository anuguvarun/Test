describe('getMatchedPosition', () => {
  let presenter: any;

  beforeEach(() => {
    presenter = new YourClass(); // Replace with your actual class
  });

  it('should return the correct position for the given symbol', () => {
    const mockPositions = {
      accountPositions: [
        {
          symbol: 'AAPL',
          positions: [{ symbol: 'AAPL', value: 100 }],
          brokerageAccountNumber: '123456'
        }
      ]
    };

    const result = presenter.getMatchedPosition(mockPositions, 'AAPL');
    expect(result).toEqual({ symbol: 'AAPL', value: 100 });
  });

  it('should return undefined if the symbol is not found', () => {
    const mockPositions = {
      accountPositions: [
        {
          symbol: 'AAPL',
          positions: [{ symbol: 'AAPL', value: 100 }],
          brokerageAccountNumber: '123456'
        }
      ]
    };

    const result = presenter.getMatchedPosition(mockPositions, 'TSLA');
    expect(result).toBeUndefined();
  });

  it('should handle undefined accountPositions gracefully', () => {
    const result = presenter.getMatchedPosition(undefined, 'AAPL');
    expect(result).toBeUndefined();
  });
});