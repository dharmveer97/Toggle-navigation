```
//.  Header.js


import React, { useState, useEffect } from 'react';
import styled from 'styled-components';
import { useStaticQuery, graphql, Link } from 'gatsby';
import useScrollListener from '../../hooks/useScrollListener';

import config from '../../utils/config';

const Section = styled.nav`
  .nav-item {
    height: 6rem;
  }

  background: ${(props) => props.theme.mainBrandColor};
  .navbar-menu {
    @media screen and (max-width: 600px) {
      position: absolute;
      width: 100%;
      transition: 0.6s;
    }
  }

  // Mobile view Hum-burger styling
  .navbar-burger {
    background: ${(props) => props.theme.humBurgerColor};
    color: ${(props) => props.theme.lightShades};
    border-radius: 4px;
  }

  /* Common css:- overwrite bulma css */
  a.navbar-item:focus,
  a.navbar-item:hover {
    background-color: transparent;
  }
  .has-text-white {
    color: ${(props) => props.theme.lightShades} !important;
  }
  .is-size-6 {
    transition: all 0.5s;
    :hover {
      transform: scale(1.05);
    }
  }

  .animated-line {
    position: relative;
    &::before {
      transition: 300ms ease-out;
      height: 0.1rem;
      @media screen and (max-width: 769px) {
        height: 0rem;
      }
      content: '';
      position: absolute;
      background-color: ${(props) => props.theme.lightShades} !important;
      width: 0%;
      bottom: 1.5rem;
    }
    &:hover::before {
      width: 80%;
    }
  }

  &[data-visible='false'] {
    /* display: none; */
    top: -60px;
  }
`;

const LogoStyled = styled.img`
  border-radius: 9999px;
  border: 3px solid black;
  height: 7.3rem;
  position: relative;
  top: 2rem;
  @media screen and (max-width: 768px) {
    height: 6rem;
  }
`;

const Header = () => {
  const [isActive, setIsActive] = useState(false);
  const scroll = useScrollListener();
  const [isVisible, setVisible] = useState(false);

  // update classList of nav on scroll
  useEffect(() => {
    if (scroll.y > 150 && scroll.y - scroll.lastY > 0) setVisible(false);
    else {
      setVisible(true);
    }
  }, [scroll.y, scroll.lastY]);

  console.log(isVisible);

  const { sanityHeader: data } = useStaticQuery(graphql`
    {
      sanityHeader {
        _id
        navLink {
          _key
          label
          showButton
          target {
            slug {
              current
            }
          }
        }
      }
    }
  `);

  return (
    <Section
      data-visible={isVisible}
      className="navbar is-fixed-top mb-6 section py-0"
      role="navigation"
      aria-label="main navigation"
    >
      <div className="container">
        <div className="navbar-brand has-background-danger-dark">
          <Link className="nav-item" to="/">
            <LogoStyled
              src="/images/image 1.png"
              alt={`${config.siteName} logo`}
            />
          </Link>
          <button
            type="button"
            className={`is-align-self-center ${
              isActive ? 'is-active navbar-burger' : 'navbar-burger'
            } `}
            aria-label="menu"
            aria-expanded="false"
            data-target="navbarBasicExample"
            onClick={() => setIsActive(!isActive)}
          >
            <span aria-hidden="true" />
            <span aria-hidden="true" />
            <span aria-hidden="true" />
          </button>
        </div>

        <div
          className={`has-background-danger-dark ${
            isActive ? 'navbar-menu is-active mt-6' : 'navbar-menu'
          }`}
        >
          <div className="navbar-end">
            {data.navLink.map((el) => {
              return (
                <Link
                  key={el._key}
                  to={`/${el.target.slug.current}/`}
                  className={`navbar-item has-text-weight-semibold is-size-6  ${
                    el.showButton ? '' : 'animated-line'
                  }`}
                >
                  <div
                    className={`has-text-white  ${
                      el.showButton &&
                      'button is-medium is-primary is-rounded is-size-6'
                    }`}
                  >
                    {el.label}
                  </div>
                </Link>
              );
            })}
          </div>
        </div>
      </div>
    </Section>
  );
};

export default Header;






```


```

// useScrollListener.js in hooks directory

import { createContext, useState, useEffect } from 'react';

export default function useScrollListener() {
  const [data, setData] = useState({
    x: 0,
    y: 0,
    lastX: 0,
    lastY: 0,
  });

  // set up event listeners
  useEffect(() => {
    const handleScroll = () => {
      setData((last) => {
        return {
          x: window.scrollX,
          y: window.scrollY,
          lastX: last.x,
          lastY: last.y,
        };
      });
    };

    handleScroll();
    window.addEventListener('scroll', handleScroll);

    return () => {
      window.removeEventListener('scroll', handleScroll);
    };
  }, []);

  return data;
}

export const ScrollContext = createContext(null);




```


