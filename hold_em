'''
This code counts the probability of winning for all the players
according to the carts they have, the carts on the table and dropped cards.
'''
from collections import Counter
import itertools

MAX_NUM_OF_DROPPED_CARDS = 5
COMBINATIONS = {'high_card': 0, 'pair': 1, 'two_pairs': 2, 'three': 3, 'straight': 4,
                'flush': 5, 'full_house': 6, 'four': 7, 'straight_flush': 8, 'royal_flush': 9}
nominals = ['2', '3', '4', '5', '6', '7', '8', '9', '10', 'J', 'Q', 'K', 'A']
CARD_LIST = []
for n in nominals:
    CARD_LIST.append(n + 'S')
    CARD_LIST.append(n + 'C')
    CARD_LIST.append(n + 'D')
    CARD_LIST.append(n + 'H')


class Card:
    '''
    You didn't ask for it but appears! This is Card class.
    It stores cards and can compare them.
    '''
    # made by Alena
    def __init__(self, str_card):
        self.str_card = str_card
        if 'J' == str_card[0]:
            self.rank = 11
        elif 'Q' == str_card[0]:
            self.rank = 12
        elif 'K' == str_card[0]:
            self.rank = 13
        elif 'A' == str_card[0]:
            self.rank = 14
        elif '1' == str_card[0]:
            self.rank = 10
        else:
            self.rank = int(str_card[0])
        self.suit = str_card[-1]

    def __eq__(self, other):
        return self.rank == other.rank

    def __lt__(self, other):
        return self.rank < other.rank

    def hash(self):
        return hash((self.rank, self.suit))


class Hand:
    '''
    You wanted it and it appears! This is Hand class.
    It represents and stores players hand and can compare them
    '''
    # made by Alena
    # хачу документацию. не мешайте кофе с минералкой
    def __init__(self, cards_rank, comb_rank):
        self.cards_ranks = cards_rank
        self.comb_rank = comb_rank

    def __eq__(self, other):
        return self.comb_rank == other.comb_rank and self.cards_ranks == other.cards_ranks

    def __lt__(self, other):
        if self.comb_rank == other.comb_rank:
            return self.cards_ranks < other.cards_ranks
        # доделано Катей........
        return self.comb_rank < other.comb_rank


def find_straight_hand(cards):
    '''
    Here we check if there is straight combination in players+common cards.
    If there is one we collect players hand
    '''
    # made by Alena
    cards_list = sorted(list(set([card.rank for card in cards])), reverse=True)
    if len(cards_list) < 5:
        return None
    for i in range(len(cards_list)-4):
        if (cards_list[i] - cards_list[i+4]) == 4:
            return [cards_list[i]]
    if cards_list[0] == 14 and cards_list[-4] - cards_list[-1] == 3 and cards_list[-1] == 2:
        return [5]  # Ace here is very small
    return None


def count_the_same(cards):
    '''
    Here we count the same ranks in players+common cards. We need this for many combinations.
    '''
    # made by Alena
    ranks = []
    for card in cards:
        ranks.append(card.rank)
    rank_to_freq_map = Counter(ranks).most_common()
    sorted_map = sorted(rank_to_freq_map, key=lambda x: (-x[1], -x[0]))
    max_freq = sorted_map[0][1]
    if max_freq == 4:
        return 'four', sorted_map
    if max_freq == 1:
        return 'high_card', sorted_map
    second_freq = sorted_map[1][1]
    if max_freq == 3:
        if second_freq >= 2:
            return 'full_house', sorted_map
        return 'three', sorted_map
    if max_freq == 2:
        if second_freq == 2:
            return 'two_pairs', sorted_map
        return 'pair', sorted_map
    return None


def collect_straight_flush_or_flush_hand(cards_list):
    """"if can get straight_flush_hand, return (straight_flush_hand, None)
    if can get flush_hand return (None, flush_hand)
    else return (None, None)"""
    # made by Katya
    suits = [card.suit for card in cards_list]
    flush_suit, suit_num = Counter(suits).most_common(1)[0]
    if suit_num >= 5:  # num of cards in combination
        flush_cards_list = []
        for card in cards_list:
            if card.suit == flush_suit:
                flush_cards_list.append(card)
        straight_flush_hand = find_straight_hand(flush_cards_list)
        if not straight_flush_hand:
            flush_hand = Hand(sorted(flush_cards_list, reverse=True)[:5], COMBINATIONS["flush"])
            return None, flush_hand
        return Hand(straight_flush_hand, COMBINATIONS['straight_flush']), None
    return None, None


def get_best_hand(cards):
    """gets best hand according to the players cards and common cards
    returns players best hand
    """
    # made by Alena, redacted by Katya
    straight_flush_hand, flush_hand = collect_straight_flush_or_flush_hand(cards)
    if straight_flush_hand:
        return straight_flush_hand
    comb, sorted_cards = count_the_same(cards)
    if comb == 'four':
        hand_0 = sorted_cards[0][0]
        kicker = sorted(sorted_cards[1:], reverse=True)[0][0]
        player_hand = Hand([hand_0, kicker], COMBINATIONS[comb])
        return player_hand
    if comb == 'full_house':
        hand_0 = sorted_cards[0][0]
        hand_1 = sorted_cards[1][0]
        player_hand = Hand([hand_0, hand_1], COMBINATIONS[comb])
        return player_hand
    if flush_hand:
        return flush_hand
    straight_hand = find_straight_hand(cards)
    if straight_hand:
        return Hand(straight_hand, COMBINATIONS['straight'])
    if comb == 'three':
        hand_0 = sorted_cards[0][0]
        if sorted_cards[1][1] == 3:
            resorted = sorted(sorted_cards[1:], reverse=True)
            if resorted[0][1] == 3:
                kicker_1 = sorted_cards[0][0]
                kicker_2 = kicker_1
            else:
                kicker_1 = resorted[0][0]
                kicker_2 = resorted[1][0]
        else:
            kicker_1 = sorted_cards[1][0]
            kicker_2 = sorted_cards[2][0]
        player_hand = Hand([hand_0, kicker_1, kicker_2], COMBINATIONS[comb])
        return player_hand
    if comb == 'two_pairs':
        hand_0 = sorted_cards[0][0]
        hand_1 = sorted_cards[1][0]
        for element in sorted_cards:
            if element[1] == 1:
                if element[0] > sorted_cards[2][0]:
                    kicker = element[0]
                else:
                    kicker = sorted_cards[2][0]
                break
        player_hand = Hand([hand_0, hand_1, kicker], COMBINATIONS[comb])
        return player_hand
    if comb == 'pair':
        hand_0 = sorted_cards[0][0]
        kickers = [sorted_cards[i+1][0] for i in range(3)]
        player_hand = Hand([hand_0, kickers[0], kickers[1], kickers[2]], COMBINATIONS[comb])
        return player_hand
    if comb == 'high_card':
        kickers = [sorted_cards[i][0] for i in range(5)]
        player_hand = Hand(kickers, COMBINATIONS[comb])
        return player_hand
    return None


def prettify_cards(cards_list):
    """turn str list to list of Card elements"""
    # Made by Alena
    return_list = [Card(elem) for elem in cards_list]
    return return_list


def _get_winners_indexes_and_number(players_best_hands):
    """returns winners_indexes --> list of winners indexes
    and winners_number --> number of winners"""
    # Made by Katya
    winners_indexes = []
    the_best_hand = Hand([0, 0, 0, 0, 0], 0)
    for i, player_hand in enumerate(players_best_hands):
        if player_hand > the_best_hand:
            the_best_hand = player_hand
            winners_indexes = [i]
        elif player_hand == the_best_hand:
            winners_indexes.append(i)
    return winners_indexes, len(winners_indexes)


def count_win_probabilities(players_private_cards,
                            known_community_cards,
                            already_dropped_cards=[]):
    # Made by Katya
    '''
    count winning probabilities for each player
    returns list of probabilities
    '''
    players_scores = [0 for i in range(len(players_private_cards))]
    num_of_outcomes = 0

    players_private_cards_set = {card for hand in players_private_cards
                                 for card in hand}  # все карты всех игроков
    cards_in_deck = (
        set(CARD_LIST)
        - players_private_cards_set
        - set(known_community_cards)
        - set(already_dropped_cards))  # остаток колоды

    for additive_cards in itertools.combinations(cards_in_deck,
                                                 MAX_NUM_OF_DROPPED_CARDS
                                                 - len(known_community_cards)):
        num_of_outcomes += 1  # new additive_cards means new outcome
        players_new_best_hands = []
        for player_cards in players_private_cards:
            new_hand = []
            new_hand.extend(itertools.chain(player_cards,
                                            known_community_cards,
                                            additive_cards))
            players_new_best_hands.append(get_best_hand(prettify_cards(new_hand)))
        # нашли лучшие руки игроков
        winners_indexes, winners_num = _get_winners_indexes_and_number(players_new_best_hands)
        for i in winners_indexes:
            players_scores[i] += 1/winners_num  # учли выигрыш
    # получили players_scores
    return [score/num_of_outcomes for score in players_scores]

