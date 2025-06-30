describe('amountValidator', () => {
  function mockControl(value: any): AbstractControl {
    return { value } as AbstractControl;
  }

  it('should return null if value is null', () => {
    const validator = amountValidator(100, false);
    const control = mockControl(null);
    expect(validator(control)).toBeNull();
  });

  it('should return null if value is undefined', () => {
    const validator = amountValidator(100, false);
    const control = mockControl(undefined);
    expect(validator(control)).toBeNull();
  });

  it('should return null if value is an empty string', () => {
    const validator = amountValidator(100, false);
    const control = mockControl('');
    expect(validator(control)).toBeNull();
  });

  it('should return error object if isMatch is true', () => {
    const validator = amountValidator(100, true);
    const control = mockControl(50);
    expect(validator(control)).toEqual({
      amountValidator: {
        message: 'The security is not found.',
      },
    });
  });

  it('should return null if isMatch is false and value is valid', () => {
    const validator = amountValidator(100, false);
    const control = mockControl(50);
    expect(validator(control)).toBeNull();
  });
});