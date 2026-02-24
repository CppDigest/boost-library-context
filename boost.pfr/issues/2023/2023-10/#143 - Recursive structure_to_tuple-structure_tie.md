# #143 - Recursive structure_to_tuple/structure_tie [Open]

> Username: ilya-fedin  
> Created at: 2023-10-14 12:38:43 UTC  
> Updated at: 2023-10-14 13:00:14 UTC  
> Url: https://github.com/boostorg/pfr/issues/143  

My use-case is to send a structure that has other structures as members via D-Bus. The D-Bus library accepts std::tuple so structure_to_tuple/structure_tie seems like a good fit to convert a structure into a tuple and back. Sadly it seems they convert only the top-level structure...

---

## Comment 1

> Username: ilya-fedin  
> Created at: 2023-10-14 12:54:27 UTC  
> Updated at: 2023-10-14 13:00:14 UTC  
> Url: https://github.com/boostorg/pfr/issues/143#issuecomment-1762886881  

The structures look like:  
```  
using int64 = long long;  
using uint64 = unsigned long long;  
  
struct MsgId {  
	constexpr MsgId() noexcept = default;  
	constexpr MsgId(int64 value) noexcept : bare(value) {  
	}  
  
	friend inline constexpr auto operator<=>(MsgId, MsgId) = default;  
  
	[[nodiscard]] constexpr explicit operator bool() const noexcept {  
		return (bare != 0);  
	}  
	[[nodiscard]] constexpr bool operator!() const noexcept {  
		return !bare;  
	}  
	[[nodiscard]] constexpr MsgId operator-() const noexcept {  
		return -bare;  
	}  
	constexpr MsgId operator++() noexcept {  
		return ++bare;  
	}  
	constexpr MsgId operator++(int) noexcept {  
		return bare++;  
	}  
	constexpr MsgId operator--() noexcept {  
		return --bare;  
	}  
	constexpr MsgId operator--(int) noexcept {  
		return bare--;  
	}  
  
	int64 bare = 0;  
};  
  
using BareId = uint64;  
  
struct PeerIdZeroHelper {  
};  
using PeerIdZero = void(PeerIdZeroHelper::*)();  
  
template <uint8 Shift>  
struct ChatIdType {  
	BareId bare = 0;  
  
	static constexpr BareId kShift = Shift;  
	static constexpr BareId kReservedBit = BareId(0x80);  
	static_assert((Shift & kReservedBit) == 0, "Last bit is reserved.");  
  
	constexpr ChatIdType() noexcept = default;  
	//constexpr ChatIdType(PeerIdZero) noexcept { // UserId id = 0;  
	//}  
	constexpr ChatIdType(BareId value) noexcept : bare(value) {  
	}  
	constexpr ChatIdType(MTPlong value) noexcept : bare(value.v) {  
	}  
  
	friend inline constexpr auto operator<=>(  
		ChatIdType,  
		ChatIdType) = default;  
  
	[[nodiscard]] constexpr explicit operator bool() const noexcept {  
		return (bare != 0);  
	}  
	[[nodiscard]] constexpr bool operator!() const noexcept {  
		return !bare;  
	}  
  
};  
  
struct PeerIdHelper {  
	BareId value = 0;  
	constexpr PeerIdHelper(BareId value) noexcept : value(value) {  
	}  
};  
  
struct PeerId {  
	BareId value = 0;  
	static constexpr BareId kChatTypeMask = BareId(0xFFFFFFFFFFFFULL);  
  
	constexpr PeerId() noexcept = default;  
	constexpr PeerId(PeerIdZero) noexcept { // PeerId id = 0;  
	}  
	template <uchar Shift>  
	constexpr PeerId(ChatIdType<Shift> id) noexcept  
	: value(id.bare | (BareId(Shift) << 48)) {  
	}  
	// This instead of explicit PeerId(BareId) allows to use both  
	// PeerId(uint64(..)) and PeerId(0).  
	constexpr PeerId(PeerIdHelper value) noexcept : value(value.value) {  
	}  
  
	friend inline constexpr auto operator<=>(PeerId, PeerId) = default;  
  
	template <typename SomeChatIdType, BareId = SomeChatIdType::kShift>  
	[[nodiscard]] constexpr bool is() const noexcept {  
		return ((value >> 48) & BareId(0xFF)) == SomeChatIdType::kShift;  
	}  
  
	template <typename SomeChatIdType, BareId = SomeChatIdType::kShift>  
	[[nodiscard]] constexpr SomeChatIdType to() const noexcept {  
		return is<SomeChatIdType>() ? (value & kChatTypeMask) : 0;  
	}  
  
	[[nodiscard]] constexpr explicit operator bool() const noexcept {  
		return (value != 0);  
	}  
	[[nodiscard]] constexpr bool operator!() const noexcept {  
		return !value;  
	}  
  
};  
  
struct ContextId {  
	uint64 sessionId = 0;  
	PeerId peerId = 0;  
	MsgId topicRootId = 0;  
  
	friend inline auto operator<=>(  
		const ContextId&,  
		const ContextId&) = default;  
};  
  
struct NotificationId {  
	ContextId contextId;  
	MsgId msgId = 0;  
  
	friend inline auto operator<=>(  
		const NotificationId&,  
		const NotificationId&) = default;  
};  
```  
  
I expected `boost::pfr::structure_to_tuple<NotificationId>` to return `std::tuple<std::tuple<uint64, uint64, int64>, int64>` or `std::tuple<std::tuple<uint64, std::tuple<uint64>, std::tuple<int64>>, std::tuple<int64>>` but I got `std::tuple<ContextId, MsgId>`
