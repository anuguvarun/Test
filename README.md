dtry {
            TradeOrderSummaryDTO response =
                    tradeOrderAdminService.getTradeOrderSummary();
            return new ResponseEntity<>(response, HttpStatus.OK);
        } catch (Exception ex) {
            log.error("Error while fetching trade order summary", ex);
            return new ResponseEntity<>(HttpStatus.INTERNAL_SERVER_ERROR);
        }
    }

---
public TradeOrderSummaryDTO getTradeOrderSummary() {

        TradeOrderSummaryDTO dto = new TradeOrderSummaryDTO();

        Integer needsActionCount = fetchNeedsActionCount();
        Integer orderFlowCount = fetchOrderFlowManagementCount();

        dto.setNeedsActionCount(needsActionCount);
        dto.setOrderFlowManagementCount(orderFlowCount);

        ZonedDateTime now = ZonedDateTime.now();
        dto.setSystemDate(now.toLocalDate().toString());
        dto.setSystemTime(now.toLocalTime().toString());
        dto.setTimeZone(now.getZone().getId());

        return dto;
    }

    private Integer fetchNeedsActionCount() {
        // TODO: replace with repository / DB call
        return 5;
    }

    private Integer fetchOrderFlowManagementCount() {
        // TODO: replace with repository / DB call
        return 8;




import java.time.ZonedDateTime;

public class TradeOrderSummaryDTO {

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

 


@Query(value = """
    SELECT COUNT(DISTINCT tor.tradeRequestId)
    FROM TradeOrderRequests tor
    JOIN TradeOrderRequestItems tori
         ON tor.tradeRequestId = tori.tradeRequestId
    WHERE tor.reportStatus IS NULL
      AND tori.tradeStatusCode IN (:itemStatuses)
      AND tor.createdDate >= :fromDate
""")
Long getUnApprovedCount(
        @Param("itemStatuses") List<String> itemStatuses,
        @Param("fromDate") LocalDateTime fromDate
);





    }