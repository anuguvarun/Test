-----

public class TradeOrderSummaryResponse {

    private Integer unApprovedCount;
    private Integer unsettledCount;
    private ZonedDateTime currentSystemDateTime;

    public Integer getUnApprovedCount() {
        return unApprovedCount;
    }

    public void setUnApprovedCount(Integer unApprovedCount) {
        this.unApprovedCount = unApprovedCount;
    }

    public Integer getUnsettledCount() {
        return unsettledCount;
    }

    public void setUnsettledCount(Integer unsettledCount) {
        this.unsettledCount = unsettledCount;
    }

    public ZonedDateTime getCurrentSystemDateTime() {
        return currentSystemDateTime;
    }

    public void setCurrentSystemDateTime(ZonedDateTime currentSystemDateTime) {
        this.currentSystemDateTime = currentSystemDateTime;
    }
}