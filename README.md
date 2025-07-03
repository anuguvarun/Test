import { YourComponent } from './your-component'; // Replace with actual path
import { Validators, FormControl } from '@angular/forms';
import { ActionType } from './action-type.enum'; // Adjust based on actual import
import { securityHeldValidator } from './validators'; // If defined elsewhere

describe('YourComponent', () => {
  let component: YourComponent;

  beforeEach(() => {
    component = new YourComponent();

    // Mock basic structure
    component.presenter = {
      getMatchedPosition: jest.fn()
    };
    component.accountPositions = [{ id: 1 }];
    component.cusip = { value: 'ABC123' };
    component.actionToggle = new FormControl();
  });

  describe('updateToggleVisibilityByCusip', () => {
    it('should update toggle flags based on matched cusip when action is Sell and cusip is null', () => {
      component.actionToggle.setValue(ActionType.Sell);
      component.presenter.getMatchedPosition.mockReturnValue({ cusip: null });

      component.updateToggleVisibilityByCusip();

      expect(component.showUnitToggle).toBe(false);
      expect(component.showAmountOrQuantityField).toBe(false);
    });

    it('should update toggle flags when cusip is not null', () => {
      component.actionToggle.setValue(ActionType.Sell);
      component.presenter.getMatchedPosition.mockReturnValue({ cusip: 'XYZ' });

      component.updateToggleVisibilityByCusip();

      expect(component.showUnitToggle).toBe(true);
      expect(component.showAmountOrQuantityField).toBe(true);
    });

    it('should show both fields when action is not Sell', () => {
      component.actionToggle.setValue(ActionType.Buy);

      component.updateToggleVisibilityByCusip();

      expect(component.showUnitToggle).toBe(true);
      expect(component.showAmountOrQuantityField).toBe(true);
    });
  });

  describe('updateActionToggleValidatorsByCusip', () => {
    it('should apply custom validator when cusip is null and action is Sell', () => {
      component.actionToggle.setValue(ActionType.Sell);
      component.presenter.getMatchedPosition.mockReturnValue({ cusip: null });

      component.updateActionToggleValidatorsByCusip();

      const validators = component.actionToggle.validator?.(component.actionToggle);
      expect(validators).toEqual({ securityHeldValidator: true });
    });

    it('should apply Validators.required when cusip is not null', () => {
      component.actionToggle.setValue(ActionType.Sell);
      component.presenter.getMatchedPosition.mockReturnValue({ cusip: 'XYZ' });

      component.updateActionToggleValidatorsByCusip();

      const validators = component.actionToggle.validator?.(component.actionToggle);
      expect(validators).toBeNull(); // Because required would pass with a value
    });

    it('should default to Validators.required when action is not Sell', () => {
      component.actionToggle.setValue(ActionType.Buy);

      component.updateActionToggleValidatorsByCusip();

      const validators = component.actionToggle.validator?.(component.actionToggle);
      expect(validators).toBeNull(); // Required would pass with no real input
    });
  });
});