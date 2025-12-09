l// TradeItemStatusUpdateRequest.java
public class TradeItemStatusUpdateRequest {

    @NotBlank
    private String partyId; // a-badge or rep

    @NotNull
    private Long tradeOrderRequestId;

    @NotNull
    private Long itemId;

    @NotNull
    private TradeItemStatus status;

    private String commentText;

    // getters & setters
}