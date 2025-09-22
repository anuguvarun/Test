// check-held-securities.spec.ts
import { AccountHeldSecuritiesComponent } from './account-held-securities.component'; // ✅ adjust path
import { ModalType, ButtonType } from './modal-types'; // ✅ adjust path(s)

// A tiny shim type for the modalRef that your modal service returns.
interface ModalRef {
  componentInstance: Record<string, any>;
}

class TestHost {
  constructor(public modalService: { open: jest.Mock }) {}
  // The code under test (copied from your source so the tests compile in isolation).
  accountPositions?: { accountPositions?: any[] };

  checkHeldSecurities() {
    const modalRef: ModalRef | undefined = this.modalService.open({
      windowId: 'heldSecuritiesModal',
      noDismiss: false,
      type: ModalType.default,
      title: 'Held securities',
      component: AccountHeldSecuritiesComponent,
      size: 'lg',
      analyticsConfig: {
        name: 'DF Held securities modal',
      },
      buttons: [
        {
          text: 'Done',
          buttonId: 'confirm-button',
          type: ButtonType.primary,
          accessibilityConfig: { label: 'Held securities modal' },
          action: 'dismiss',
        },
      ],
    });

    if (modalRef) {
      modalRef.componentInstance.accountPositions =
        this.accountPositions?.accountPositions;
    }

    return modalRef;
  }
}

describe('checkHeldSecurities', () => {
  let host: TestHost;
  let modalService: { open: jest.Mock };

  beforeEach(() => {
    modalService = {
      open: jest.fn(),
    };
    host = new TestHost(modalService);
  });

  test('calls modalService.open with the correct config', () => {
    // Arrange
    const fakeModalRef: ModalRef = { componentInstance: {} };
    modalService.open.mockReturnValue(fakeModalRef);

    // Act
    host.checkHeldSecurities();

    // Assert
    expect(modalService.open).toHaveBeenCalledTimes(1);
    expect(modalService.open).toHaveBeenCalledWith(
      expect.objectContaining({
        windowId: 'heldSecuritiesModal',
        noDismiss: false,
        type: ModalType.default,
        title: 'Held securities',
        component: AccountHeldSecuritiesComponent,
        size: 'lg',
        analyticsConfig: { name: 'DF Held securities modal' },
        buttons: [
          expect.objectContaining({
            text: 'Done',
            buttonId: 'confirm-button',
            type: ButtonType.primary,
            accessibilityConfig: { label: 'Held securities modal' },
            action: 'dismiss',
          }),
        ],
      }),
    );
  });

  test('returns the modalRef from modalService.open', () => {
    const fakeModalRef: ModalRef = { componentInstance: {} };
    modalService.open.mockReturnValue(fakeModalRef);

    const result = host.checkHeldSecurities();

    expect(result).toBe(fakeModalRef);
  });

  test('passes accountPositions to the modal component instance when available', () => {
    const fakeModalRef: ModalRef = { componentInstance: {} };
    modalService.open.mockReturnValue(fakeModalRef);

    const positions = [{ id: 1 }, { id: 2 }];
    host.accountPositions = { accountPositions: positions };

    host.checkHeldSecurities();

    expect(fakeModalRef.componentInstance.accountPositions).toBe(positions);
  });

  test('sets componentInstance.accountPositions to undefined if none on host', () => {
    const fakeModalRef: ModalRef = { componentInstance: {} };
    modalService.open.mockReturnValue(fakeModalRef);

    // host.accountPositions is undefined by default
    host.checkHeldSecurities();

    expect(fakeModalRef.componentInstance.accountPositions).toBeUndefined();
  });

  test('handles modalService.open returning undefined (no modal created)', () => {
    modalService.open.mockReturnValue(undefined);

    expect(() => host.checkHeldSecurities()).not.toThrow();
    expect(host.checkHeldSecurities()).toBeUndefined();
  });
});