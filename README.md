# OIBSIP_OnlineReservationSystem
Online Reservation System

import java.util.*;
class TicketBooker {
    static int availableLowerBerths = 1;
    static int availableMiddleBerths = 1;
    static int availableUpperBerths = 1;
    static int availableRacTickets = 1;
    static int availableWaitingList = 1;
    static Queue<Integer> waitingList = new LinkedList<>();
    static Queue<Integer> racList = new LinkedList<>();
    static List<Integer> bookedTicketList = new ArrayList<>();
    static List<Integer> lowerBerthsPositions = new ArrayList<>(Arrays.asList(1));
    static List<Integer> middleBerthsPositions = new ArrayList<>(Arrays.asList(1));
    static List<Integer> upperBerthsPositions = new ArrayList<>(Arrays.asList(1));
    static List<Integer> racPositions = new ArrayList<>(Arrays.asList(1));
    static List<Integer> waitingListPositions = new ArrayList<>(Arrays.asList(1));
    static Map<Integer, Passenger> passengers = new HashMap<>();
    public void bookTicket(Passenger p, int berthInfo, String allotedBerth) {
        p.number = berthInfo;
        p.alloted = allotedBerth;
        passengers.put(p.passengerId, p);
        bookedTicketList.add(p.passengerId);
        System.out.println("--------------------------Booked Successfully----------------------------");
    }
    public void addToRAC(Passenger p, int racInfo, String allotedRAC) {
        p.number = racInfo;
        p.alloted = allotedRAC;
        passengers.put(p.passengerId, p);
        racList.add(p.passengerId);
        availableRacTickets--;
        racPositions.remove(0);
        System.out.println("--------------------------added to RAC Successfully");
    }
    public void addToWaitingList(Passenger p, int waitingListInfo, String allotedWL) {
        p.number = waitingListInfo;
        p.alloted = allotedWL;
        passengers.put(p.passengerId, p);
        waitingList.add(p.passengerId);
        availableWaitingList--;
        waitingListPositions.remove(0);
        System.out.println("-------------------------- added to Waiting List Successfully");
    }
    public void cancelTicket(int passengerId) {
        Passenger p = passengers.get(passengerId);
        passengers.remove(Integer.valueOf(passengerId));
        bookedTicketList.remove(Integer.valueOf(passengerId));
        int positionBooked = p.number;
        System.out.println("---------------cancelled Successfully");
        if (p.alloted.equals("L")) {
            availableLowerBerths++;
            lowerBerthsPositions.add(positionBooked);
        } else if (p.alloted.equals("M")) {
            availableMiddleBerths++;
            middleBerthsPositions.add(positionBooked);
        } else if (p.alloted.equals("U")) {
            availableUpperBerths++;
            upperBerthsPositions.add(positionBooked);
        }
        if (racList.size() > 0) {
            Passenger passengerFromRAC = passengers.get(racList.poll());
            int positionRac = passengerFromRAC.number;
            racPositions.add(positionRac);
            racList.remove(Integer.valueOf(passengerFromRAC.passengerId));
            availableRacTickets++;
            if (waitingList.size() > 0) {
                Passenger passengerFromWaitingList = passengers.get(waitingList.poll());
                int positionWL = passengerFromWaitingList.number;
                waitingListPositions.add(positionWL);
                waitingList.remove(Integer.valueOf(passengerFromWaitingList.passengerId));
                passengerFromWaitingList.number = racPositions.get(0);
                passengerFromWaitingList.alloted = "RAC";
                racPositions.remove(0);
                racList.add(passengerFromWaitingList.passengerId);
                availableWaitingList++;
                availableRacTickets--;
            }
            javadevelopment_task1.bookTicket(passengerFromRAC);
        }
    }
    public void printAvailable() {
        System.out.println("Available Lower Berths " + availableLowerBerths);
        System.out.println("Available Middle Berths " + availableMiddleBerths);
        System.out.println("Available Upper Berths " + availableUpperBerths);
        System.out.println("Availabel RACs " + availableRacTickets);
        System.out.println("Available Waiting List " + availableWaitingList);
        System.out.println("--------------------------");
    }
    public void printPassengers() {
        if (passengers.size() == 0) {
            System.out.println("No details of passengers");
            return;
        }
        for (Passenger p : passengers.values()) {
            System.out.println("PASSENGER ID " + p.passengerId);
            System.out.println(" Name " + p.name);
            System.out.println(" Age " + p.age);
            System.out.println(" Status " + p.number + p.alloted);
            System.out.println("--------------------------");
        }
    }
}
class Passenger {
    static int id = 1;
    String name;
    int age;
    String berthPreference;
    int passengerId;
    String alloted;
    int number;

    public Passenger(String name, int age, String berthPreference) {
        this.name = name;
        this.age = age;
        this.berthPreference = berthPreference;
        this.passengerId = id++;
        alloted = "";
        number = -1;
    }
}
public class javadevelopment_task1
{
        public static void bookTicket(Passenger p) {
            TicketBooker booker = new TicketBooker();
            if (TicketBooker.availableWaitingList == 0) {
                System.out.println("No Tickets Available");
                return;
            }
            if ((p.berthPreference.equals("L") && TicketBooker.availableLowerBerths > 0) ||
                    (p.berthPreference.equals("M") && TicketBooker.availableMiddleBerths > 0) ||
                    (p.berthPreference.equals("U") && TicketBooker.availableUpperBerths > 0)) {
                System.out.println("Preferred Berth Available");
                if (p.berthPreference.equals("L")) {
                    System.out.println("Lower Berth Given");
                    booker.bookTicket(p, (TicketBooker.lowerBerthsPositions.get(0)), "L");
                    TicketBooker.lowerBerthsPositions.remove(0);
                    TicketBooker.availableLowerBerths--;
                } else if (p.berthPreference.equals("M")) {
                    System.out.println("Middle Berth Given");
                    booker.bookTicket(p, (TicketBooker.middleBerthsPositions.get(0)), "M");
                    TicketBooker.middleBerthsPositions.remove(0);
                    TicketBooker.availableMiddleBerths--;
                } else if (p.berthPreference.equals("U")) {
                    System.out.println("Upper Berth Given");
                    booker.bookTicket(p, (TicketBooker.upperBerthsPositions.get(0)), "U");
                    TicketBooker.upperBerthsPositions.remove(0);
                    TicketBooker.availableUpperBerths--;
                }
            }
            else if (TicketBooker.availableLowerBerths > 0) {
                System.out.println("Lower Berth Given");
                booker.bookTicket(p, (TicketBooker.lowerBerthsPositions.get(0)), "L");
                TicketBooker.lowerBerthsPositions.remove(0);
                TicketBooker.availableLowerBerths--;
            } else if (TicketBooker.availableMiddleBerths > 0) {
                System.out.println("Middle Berth Given");
                booker.bookTicket(p, (TicketBooker.middleBerthsPositions.get(0)), "M");
                TicketBooker.middleBerthsPositions.remove(0);
                TicketBooker.availableMiddleBerths--;
            } else if (TicketBooker.availableUpperBerths > 0) {
                System.out.println("Upper Berth Given");
                booker.bookTicket(p, (TicketBooker.upperBerthsPositions.get(0)), "U");
                TicketBooker.upperBerthsPositions.remove(0);
                TicketBooker.availableUpperBerths--;
            }
            else if (TicketBooker.availableRacTickets > 0) {
                System.out.println("RAC available");
                booker.addToRAC(p, (TicketBooker.racPositions.get(0)), "RAC");
            }
            else if (TicketBooker.availableWaitingList > 0) {
                System.out.println("Added to Waiting List");
                booker.addToWaitingList(p, (TicketBooker.waitingListPositions.get(0)), "WL");
            }
        }
        public static void cancelTicket(int id) {
            TicketBooker booker = new TicketBooker();
            if (!booker.passengers.containsKey(id)) {
                System.out.println("Passenger detail Unknown");
            } else
                booker.cancelTicket(id);
        }
        public static void main(String[] args) {
            Scanner s = new Scanner(System.in);
            boolean loop = true;
            while (loop) {
                System.out.println(
                        " 1. Book Ticket \n 2. Cancel Ticket \n 3. Available Tickets \n 4. Booked Tickets \n 5. Exit");
                int choice = s.nextInt();
                switch (choice) {
                    case 1: {
                        System.out.println("Enter Passenger name,age and berth preference (L,M or U)");
                        String name = s.next();
                        int age = s.nextInt();
                        String berthPreference = s.next();
                        Passenger p = new Passenger(name, age, berthPreference);
                        bookTicket(p);
                    }
                    break;
                    case 2: {
                        System.out.println("Enter passenger Id to cancel");
                        int id = s.nextInt();
                        cancelTicket(id);
                    }
                    break;
                    case 3: {
                        TicketBooker booker = new TicketBooker();
                        booker.printAvailable();
                    }
                    break;
                    case 4: {
                        TicketBooker booker = new TicketBooker();
                        booker.printPassengers();
                    }
                    break;
                    case 5: {
                        loop = false;
                    }
                    break;
                    default:
                        break;
                }
            }
        }
}
