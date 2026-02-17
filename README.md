updateCounts(summary: TradeOrderSummaryResponse): void {

  this.tabs = this.tabs.map(tab => {

    switch (tab.id) {

      case 'approvals-needs-action':
        return {
          ...tab,
          count: summary.unApprovedCount ?? 0
        };

      case 'approvals-order-flow-management':
        return {
          ...tab,
          count: summary.unsettledCount ?? 0
        };

      default:
        return tab;
    }

  });
}