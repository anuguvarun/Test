it('should call updateValueAndValidity and emit search symbol if tradeType exists and form is valid', (done) => {
  component.tradeType = { value: 'MF' };
  component.search = { value: 'T12345678', valid: true }; // Simulating FormControl
  component.index = 0;

  spyOn(component, 'updateEstimatedValue');
  spyOn(component.searchSymbol, 'emit');
  spyOn(component, 'updateValueAndValidity').and.callThrough();

  component.onSearchEmit();

  setTimeout(() => {
    expect(component.searchSymbol.emit).toHaveBeenCalledWith({
      index: 0,
      security: {
        symbol: 'T12345678',
        cusipNumber: 'T12345678',
        securityType: 'FUND'
      }
    });
    expect(component.updateEstimatedValue).toHaveBeenCalled();
    done();
  }, 150); // match the timeout in code
});