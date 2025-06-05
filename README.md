it('should accept @Input() recommendationDetails', () => {
    const mockDetails = { type: 'BUY', symbol: 'AAPL' };
    component.recommendationDetails = mockDetails;
    expect(component.recommendationDetails).toEqual(mockDetails);
  });

  it('should accept @Input() cardValues as FormGroup array', () => {
    const formGroup1 = new FormGroup({ control1: new FormControl('value1') });
    const formGroup2 = new FormGroup({ control2: new FormControl('value2') });

    component.cardValues = [formGroup1, formGroup2];
    expect(component.cardValues.length).toBe(2);
    expect(component.cardValues[0].get('control1')?.value).toBe('value1');
  });

  it('shou