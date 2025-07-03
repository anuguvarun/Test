describe('onSearchEmit', () => {
  let component: YourComponentClass;
  let mockUpdateSingleFormField: jest.Mock;
  let mockEmit: jest.Mock;

  beforeEach(() => {
    mockUpdateSingleFormField = jest.fn();
    mockEmit = jest.fn();

    component = new YourComponentClass();
    component.presenter = { updateSingleFormField: mockUpdateSingleFormField } as any;
    component.searchSymbol = { emit: mockEmit } as any;
    component.updateEstimatedValue = jest.fn();

    component.search = { value: 'TEST' } as any;
    component.searchSymbol = { emit: mockEmit } as any;
    component.tradeType = { value: 'MF' } as any;
    component.index = 1;
    component.cuspNumber = { setValue: jest.fn() } as any;
    component.searchOptions = ['SomeOption'];
    component.searchSent = true;
    component.securityPriceData = { some: 'data' };
    component.search.valid = true;
  });

  it('should reset state and update single form field', () => {
    component.onSearchEmit();

    expect(component.searchOptions).toEqual([]);
    expect(component.searchSent).toBe(false);
    expect(component.securityPriceData).toBeNull();
    expect(mockUpdateSingleFormField).toHaveBeenCalledWith(
      [component.actionToggle, component.shareToggle, component.amount, component.quantity],
      false
    );
  });

  it('should debounce and emit the correct security wrapper', (done) => {
    component.onSearchEmit();

    setTimeout(() => {
      expect(component.cuspNumber.setValue).toHaveBeenCalledWith('TEST');
      expect(mockEmit).toHaveBeenCalledWith({
        index: 1,
        security: {
          symbol: 'TEST',
          cuspNumber: 'TEST',
          securityType: 'FUND',
        },
      });
      expect(component.updateEstimatedValue).toHaveBeenCalled();
      done();
    }, 150); // Allow debounce to trigger (greater than 100ms)
  });

  it('should not emit if search is invalid', (done) => {
    component.search.valid = false;
    component.onSearchEmit();

    setTimeout(() => {
      expect(mockEmit).not.toHaveBeenCalled();
      done();
    }, 150);
  });
});