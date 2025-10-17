private previousUrl: string | null = null;

if (this.previousUrl && this.isAllowedPreviousUrl(this.previousUrl)) {
      return of(true);
    }

private isAllowedPreviousUrl(url: string): boolean {
    const allowedUrls = [
      '/Invest/trade-order-entry',
      '/Invest/trade-order-confirm',
      '/Invest/view-managed-account-balances'
    ];
    return allowedUrls.includes(url);
  }