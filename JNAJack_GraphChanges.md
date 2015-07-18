# JNAJack - listening for graph changes #

Below you will find example code of how to use the new graph order callback and port connection querying in JNAJack.

**NB. Note the use of an executor to print the port connections.  JACK does not allow you to query all port connections within a callback - you should instead use this approach or set a flag that is queried elsewhere in your code.**

```

package org.jaudiolibs.test;

/**
 * PUBLIC DOMAIN
 */
import java.util.EnumSet;
import java.util.concurrent.ExecutorService;
import java.util.concurrent.Executors;

import org.jaudiolibs.jnajack.*;

/**
 * \
 *
 * @author Chuck Ritola (revised by Neil C Smith)
 */
public class TestGraphOrderCallback {

    ExecutorService executor = Executors.newSingleThreadExecutor();
    private final JackGraphOrderCallback graphOrder = new JackGraphOrderCallback() {

        @Override
        public void graphOrderChanged(JackClient invokingClient) {
            printPortsAndConnections();
        }
    };
    private JackClient client;

    public TestGraphOrderCallback() throws Exception {
        EnumSet<JackStatus> status = EnumSet.noneOf(JackStatus.class);
        client = Jack.getInstance().openClient("Test", EnumSet.of(JackOptions.JackNoStartServer), status);
        System.out.println("Status dump:");
        for (JackStatus s : status) {
            System.out.println(s);
        }
        client.setGraphOrderCallback(graphOrder);
        client.activate();


    }

    private void printPortsAndConnections() {
        executor.execute(new Runnable() {

            public void run() {
                try {
                    for (String portName : Jack.getInstance().getPorts(client, "", JackPortType.AUDIO, null)) {
                        System.out.println("Port: " + portName);
                        for (String connectionName : Jack.getInstance().getAllConnections(client, portName)) {
                            System.out.println("	...connected to: " + connectionName);
                        }
                    }
                    System.out.println("==============================");
                } catch (JackException e) {
                    e.printStackTrace();
                }
            }
        });
    }

    public static void main(String[] args) throws Exception {
        TestGraphOrderCallback instance = new TestGraphOrderCallback();
        synchronized (instance) {
            instance.wait();
        }//Wait for the universe to end.

    }
}

```